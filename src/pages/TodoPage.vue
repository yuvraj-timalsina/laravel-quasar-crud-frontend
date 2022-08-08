<template>
  <q-page padding>
    <div class="q-pa-md">
      <div class="q-pa-md q-gutter-sm" />
      <q-table
        :columns="columns"
        :loading="loading"
        :rows="rows"
        row-key="id"
        title="Todo"
      >
        <template #top>
          <q-btn
            color="primary"
            icon="add"
            round
            @click="todoDialog"
          />
        </template>
        <template #body="props">
          <q-tr :props="props">
            <q-td
              key="id"
              :props="props"
            >
              {{ props.row.index }}
            </q-td>
            <q-td
              key="title"
              :props="props"
            >
              {{ props.row.title }}
            </q-td>
            <q-td
              key="description"
              :props="props"
            >
              {{ props.row.description }}
            </q-td>
            <q-td
              key="actions"
              :props="props"
            >
              <q-btn
                class="text-red q-mr-sm"
                icon="delete_outline"
                name="delete"
                round
                @click="todoDelete(props.row.id)"
              />
              <q-btn
                class="text-primary"
                icon="edit_note"
                name="edit"
                round
                @click="todoEdit(props.row.id)"
              />
            </q-td>
          </q-tr>
        </template>
      </q-table>
      <q-dialog
        v-model="showDialog"
      >
        <q-card
          class="full-width"
          style="width: 37.5rem;"
        >
          <q-toolbar>
            <q-toolbar-title>{{ !todoForm.id ? 'Create Todo' : 'Edit Todo' }}</q-toolbar-title>
            <q-btn
              v-close-popup
              flat
              icon="close"
              round
              size="sm"
            />
          </q-toolbar>
          <q-card-section>
            <q-input
              v-model="todoForm.title"
              class="q-mb-md"
              filled
              label="Title"
              type="text"
            />

            <q-input
              v-model="todoForm.description"
              class="text-negative"
              filled
              label="Description"
              type="textarea"
            />
            <div class="q-mt-md">
              <q-btn
                :label="!todoForm.id ? 'Save':'Update'"
                class="full-width"
                color="primary"
                @click="!todoForm.id ? todoSave() : todoUpdate(todoForm.id)"
              />
            </div>
          </q-card-section>
        </q-card>
      </q-dialog>
    </div>
  </q-page>
</template>
<script setup>
import { api } from 'boot/axios'
import { onMounted, ref } from 'vue'
import { useQuasar } from 'quasar'

const rows = ref()
const loading = ref(true)
const showDialog = ref(false)
const $q = useQuasar()
/** get todos */
onMounted(() => {
  todoGet()
})

function todoGet () {
  api.get('todos')
    .then(res => {
      rows.value = res.data.data
      rows.value.forEach((val, index) => {
        val.index = index + 1
      })
    })
    .catch(err => {
      console.log(err)
    })
    .finally(() => {
      loading.value = false
    })
}

/** todo form dialog */
const todoForm = ref({
  title: '',
  description: ''
})
/** dialog todo */
const todoDialog = () => {
  todoForm.value = {}
  showDialog.value = true
}

/** save todo form data */
async function todoSave () {
  await api.post('todos', {
    title: todoForm.value.title,
    description: todoForm.value.description
  })
    .then(res => {
      if (res.status === 201) {
        showDialog.value = false
      }
    })
    .catch(err => {
      console.log(err)
    })
  $q.notify({
    message: 'Todo Created!',
    position: 'top',
    icon: 'thumb_up',
    color: 'positive'
  })
  todoGet()
}

/** update todo */
const todoUpdate = (id) => {
  api.put('/todos/' + id, {
    title: todoForm.value.title,
    description: todoForm.value.description
  }).then(res => {
    todoGet()
    showDialog.value = false
    console.log('Todo Updated!')
  })
  $q.notify({
    message: 'Todo Updated!',
    position: 'top',
    icon: 'tag_faces',
    color: 'info'
  })
}

/** delete todo */
const todoDelete = (id) => {
  api.delete('/todos/' + id).then(res => {
    todoGet()
    console.log('Todo Deleted!')
  })
  $q.notify({
    message: 'Todo Deleted!',
    position: 'top',
    icon: 'thumb_down',
    color: 'negative'
  })
}

/** edit todo */
const todoEdit = (id) => {
  api.get('/todos/' + id).then(res => {
    showDialog.value = true
    todoForm.value = res.data.data
  })
}

/** q-table */
const columns = [
  {
    name: 'id',
    required: true,
    label: 'ID',
    align: 'left',
    field: row => row.index,
    sortable: true
  },
  {
    name: 'title',
    required: true,
    label: 'Title',
    align: 'left',
    field: row => row.title,
    sortable: true
  },
  {
    name: 'description',
    align: 'left',
    label: 'Description',
    field: row => row.description,
    sortable: true
  },
  {
    name: 'actions',
    align: 'center',
    label: 'Actions',
    field: 'actions',
    sortable: true
  }
]

</script>

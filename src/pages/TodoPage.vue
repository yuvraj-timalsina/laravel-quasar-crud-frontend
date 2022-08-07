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
              {{ props.row.id }}
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
              filled
              label="Description"
              type="textarea"
            />
          </q-card-section>
          <q-btn
            v-if="todoForm.id"
            class="full-width"
            color="primary"
            label="Update"
            @click="todoUpdate"
          />
          <q-btn
            v-else
            class="full-width"
            color="primary"
            label="Save"
            @click="todoSave"
          />
        </q-card>
      </q-dialog>
    </div>
  </q-page>
</template>

<script setup>

import { onMounted, ref } from 'vue'
import { api } from 'boot/axios'

const showDialog = ref(false)
const loading = ref(true)
const rows = ref()

/** get todos */
onMounted(() => {
  todoGet()
})

function todoGet () {
  api.get('todos')
    .then(response => {
      rows.value = response.data.data
    })
    .catch(error => {
      console.log(error)
    })
    .finally(() => {
      loading.value = false
    })
}

/** todo form */
const todoForm = ref({
  title: '',
  description: ''
})

/** save todo form data */
async function todoSave () {
  await api.post('todos', {
    title: todoForm.value.title,
    description: todoForm.value.description
  })
    .catch(error => {
      console.log(error)
    })
  todoGet()
  showDialog.value = false
}

/** delete todo */
const todoDelete = (id) => {
  api.delete('/todos/' + id).then(res => {
    todoGet()
    console.log('Todo Deleted!')
  })
}

/** edit todo */
const todoEdit = (id) => {
  api.get('/todos/' + id).then(res => {
    showDialog.value = true
    todoForm.value = res.data.data
  })
}
/** update todo */
const todoUpdate = (id) => {
  api.put('/todos/' + id).then(res => {
    todoGet()
    console.log('Todo Updated!')
  })
}
const todoDialog = () => {
  todoForm.value = {}
  showDialog.value = true
}
/** q-table */
const columns = [
  {
    name: 'id',
    required: true,
    label: 'ID',
    align: 'left',
    field: row => row.id,
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

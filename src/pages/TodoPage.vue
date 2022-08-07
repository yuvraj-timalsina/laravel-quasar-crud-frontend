<template>
  <q-page padding>
    <div class="q-pa-md">
      <div class="q-pa-md q-gutter-sm" />
      <q-table
        :columns="columns"
        :loading="loading"
        :rows="rows"
        row-key="name"
        title="Todo"
      >
        <template #top>
          <q-btn
            color="primary"
            icon="add"
            round
            @click="showDialog=true"
          />
        </template>
        <template #body="props">
          <q-tr :props="props">
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
                @click="confirmDelete(props.row.id)"
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
            <q-toolbar-title>Create Todo</q-toolbar-title>
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
              v-model="createTodoForm.title"
              class="q-mb-md"
              filled
              label="Title"
              type="text"
            />
            <q-input
              v-model="createTodoForm.description"
              filled
              label="Description"
              type="textarea"
            />
          </q-card-section>

          <q-btn
            class="full-width"
            color="primary"
            label="Save"
            @click="saveTodo"
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
const editTodoData = ref([])

onMounted(() => {
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
})

const createTodoForm = ref({
  title: '',
  description: ''
})

async function saveTodo () {
  await api.post('todos', {
    title: createTodoForm.value.title,
    description: createTodoForm.value.description
  })
    .catch(error => {
      console.log(error)
    })
  showDialog.value = false
}

const confirmDelete = (id) => {
  api.delete('/todos/' + id).then(res => {
    console.log('Todo Deleted!')
  })
}

const todoEdit = (id) => {
  api.get('/todos/' + id).then(res => {
    showDialog.value = true
    editTodoData.value = res.data.data
  })
}

const columns = [
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
    align: 'left',
    label: 'Actions',
    field: 'actions',
    sortable: true
  }
]

</script>

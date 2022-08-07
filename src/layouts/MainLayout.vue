<template>
  <q-layout view="hHh lpR fFf">
    <q-header
      class="bg-primary text-white"
      elevated
    >
      <q-toolbar>
        <q-btn
          dense
          flat
          icon="menu"
          round
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          <q-avatar>
            <img
              alt=""
              src="https://cdn.quasar.dev/logo-v2/svg/logo-mono-white.svg"
            >
          </q-avatar>
          Quasar Todo
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      bordered
      show-if-above
      side="left"
    >
      <!-- drawer content -->
      <div
        class="q-pa-md"
        style="max-width: 350px"
      >
        <q-list bordered>
          <q-item
            v-ripple
            :to="{name:'Todo'}"
            clickable
          >
            <q-item-section avatar>
              <q-icon
                color="primary"
                name="style"
              />
            </q-item-section>
            <q-item-section>Todo</q-item-section>
          </q-item>
        </q-list>
      </div>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref } from 'vue'

export default {
  setup () {
    const leftDrawerOpen = ref(false)

    return {
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
}
</script>

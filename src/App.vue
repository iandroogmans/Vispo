<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { RouterLink, RouterView, useRouter } from 'vue-router'
import Menubar from 'primevue/menubar'
import type { MenuItem } from 'primevue/MenuItem'
import Menu from 'primevue/Menu'

import { supabase } from './supabase'
import Avatar from 'primevue/avatar'

const session = ref()
const router = useRouter()

onMounted(() => {
  supabase.auth.getSession().then(({ data }) => {
    session.value = data.session
  })

  supabase.auth.onAuthStateChange((_, _session) => {
    session.value = _session
  })
})

const navItems = ref([
  { route: { name: 'home' }, label: 'Home' },
  { route: { name: 'games' }, label: 'Games' },
  { route: { name: 'auth' }, label: 'Auth' },
])

// User menu
const accountMenu = ref()
const accountItems = ref([
  {
    label: 'Options',
    items: [
      {
        label: 'Account',
        icon: 'pi pi-user',
        route: { name: 'account' },
      },
      {
        label: 'Logout',
        icon: 'pi pi-sign-out',
      },
      {
        separator: true,
      },
    ],
  },
])

const toggleAccountMenu = (event) => {
  accountMenu.value.toggle(event)
}
</script>

<template>
  <Menubar :model="navItems">
    <template #item="{ item, props, hasSubmenu }">
      <router-link v-slot="{ href, navigate }" :to="item.route" custom>
        <a :href="href" v-bind="props.action" @click="navigate">
          <span>{{ item.label }}</span>
        </a>
      </router-link>
    </template>
    <template #end>
      <div class="flex items-center gap-2">
        <div v-if="session">
          <Avatar
            :label="session.user.email.substring(0, 1).toUpperCase()"
            @click="toggleAccountMenu"
            class="cursor-pointer"
          />
          <Menu ref="accountMenu" id="overlay_menu" :model="accountItems" :popup="true">
            <template #end>
              <button
                class="relative overflow-hidden w-full border-0 bg-transparent flex items-start p-2 pl-4 hover:bg-surface-100 dark:hover:bg-surface-800 rounded-none cursor-pointer transition-colors duration-200"
              >
                <Avatar
                  :label="session.user.email.substring(0, 1).toUpperCase()"
                  class="mr-2"
                  shape="circle"
                />
                <span class="inline-flex flex-col items-start">
                  <span class="font-bold">{{ session.user.email }}</span>
                  <!-- <span class="text-sm">Admin</span> -->
                </span>
              </button>
            </template>
          </Menu>
        </div>
        <Avatar icon="pi pi-sign-in" v-else />
      </div>
    </template>
  </Menubar>
  <RouterView />
</template>

<style scoped></style>

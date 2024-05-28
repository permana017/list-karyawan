<template>
  <v-layout class="bg-slate-100">
    <v-app-bar>
      <div class="w-full p-4" @click="drawer = !drawer">
        <v-icon class="cursor-pointer" :class="{ 'rotate-180': !drawer }"
          >mdi-backburger</v-icon
        >
      </div>
    </v-app-bar>
    <v-navigation-drawer v-model="drawer">
      <v-list>
        <v-list-item title="Navigation drawer"></v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-main class="min-h-[100vh]">
      <main class="p-5">
        <slot />
      </main>
    </v-main>
  </v-layout>
</template>
<script setup>
import { ref } from "vue";

const drawer = ref(true);
</script>

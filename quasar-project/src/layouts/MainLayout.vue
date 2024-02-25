<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar id="toolbar">
        <q-toolbar-title> Quasar Weather App </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";

export default {
  defineComponent() {
    name: "MainLayout";
  },
};
</script>

<template>
  <v-app>
    <DefaultLayout/>
  </v-app>
</template>

<script>
import DefaultLayout from './layouts/default/DefaultLayout.vue';

export default {
  name: 'App',

  components: {
    DefaultLayout,
  },

  data: () => ({
    //
  }),
};
</script>

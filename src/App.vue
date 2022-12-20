<template>
  <v-app>
 <DashBoardVue />
  </v-app>
</template>

<script>

import DashBoardVue from './components/DashBoard';

export default {
  name: 'App',

  components: {
    DashBoardVue,
  },

  data: () => ({
    //
  }),
};
</script>

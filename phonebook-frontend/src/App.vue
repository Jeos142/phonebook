<template>
  <v-app>
    <UserTable />
  </v-app>
</template>

<script>
import UserTable from './components/UserTable.vue';

export default {
  components: {
    UserTable,
  },
};
</script>
<template>
  <v-container fluid>
    <!-- <v-switch v-model="switch1"></v-switch> -->
    <v-switch :label="innerLabel" />
  </v-container>
</template>

<script>
export default {
  name: "RowSwitch",
  data() {
    return {
      switch1: true,
      switch2: false
    };
  },
  props: {
    innerLabel: String
  }
};
</script>

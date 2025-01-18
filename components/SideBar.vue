<template>
  <v-container>
    <div style="display: flex; justify-content: center">
      <h2 style="font-size: 1.5em">Category</h2>
    </div>
    <div style="display: flex; justify-content: center; padding-top: 30px">
      <v-radio-group v-model="seletedCategories">
        <v-radio
          v-for="(category, index) in categories"
          :key="index"
          :label="category"
          :value="category"
        ></v-radio>
      </v-radio-group>
    </div>
  </v-container>
</template>

<script>
export default {
  props: {
    categories: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      seletedCategories: "",
    };
  },
};
</script>

<style></style>

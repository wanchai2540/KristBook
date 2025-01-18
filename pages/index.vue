<template>
  <div>
    <NavBar />
    <v-container style="width: 100%; margin: 0 0">
      <v-row class="">
        <v-col class="" cols="3">
          <div style="display: flex; justify-content: center">
            <h2 style="font-size: 1.5em">Category</h2>
          </div>
          <div style="display: flex; justify-content: center; padding-top: 30px">
            <v-radio-group v-model="selectedCategories">
              <v-radio
                v-for="(category, index) in categories"
                :key="index"
                :label="category"
                :value="category"
              ></v-radio>
            </v-radio-group>
          </div>
        </v-col>
        <v-col cols="9">
          <div>Column 2</div>
        </v-col>
      </v-row>
    </v-container>
    <Footer />
  </div>
</template>

<script>
import CardItem from "@/components/CardItems.vue";
import NavBar from "@/components/NavBar.vue";
import SideBar from "@/components/SideBar.vue";

export default {
  name: "index",
  data() {
    return {
      originDataBook: {},
      book: [],
      categories: [],
      selectedCategories: "",
    };
  },
  async fetch() {
    var url = "https://api.gameliveball.com/items";
    try {
      const books = await this.$axios.$get(url);
      const typeBook = this.getCategories(books.data);
      this.categories = typeBook;
      const orign = this.dataEachCategaories(books.data);
      return { book: books.data };
    } catch (error) {
      console.log(error);
      return { book: [], categories: [] };
    }
  },
  watch: {
    selectedCategories(newVal) {
      // console.log("Selected category:", newVal);
      console.log(this.book);
    },
  },
  methods: {
    getCategories(books) {
      const typeBook = [];
      typeBook.push("All");
      books.forEach((bookItem) => {
        if (!typeBook.includes(bookItem.category)) {
          typeBook.push(bookItem.category);
        }
      });
      return typeBook;
    },
    dataEachCategaories(books) {
      const booksByCategory = books.reduce((acc, book) => {
        const { category } = book;
        if (!acc[category]) {
          acc[category] = [];
        }
        acc[category].push(book);
        return acc;
      }, {});
      console.log(booksByCategory);
    },
  },
};
</script>

<style>
body {
  font-family: "Roboto", sans-serif;
}
</style>

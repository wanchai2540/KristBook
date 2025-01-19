<template>
  <div data-app>
    <v-app>
      <v-main>
        <div>
          <NavBar :cart="cart" />
          <v-container style="width: 100%; margin: 0 0">
            <v-row class="">
              <v-col class="" cols="3">
                <div style="display: flex; justify-content: center">
                  <h2>Category</h2>
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
                <v-col cols="12">results: {{ filteredBooks.length }}</v-col>
                <v-row>
                  <v-col v-for="(book, index) in filteredBooks" :key="index" cols="4">
                    <v-col
                      cols="12"
                      style="
                        padding: 1.5em;
                        background-color: rgba(0, 0, 0, 3%);
                        margin-bottom: 10px;
                      "
                    >
                      <v-img :src="book.photolink" contain></v-img>
                    </v-col>
                    <v-row>
                      <v-col cols="12" style="min-height: 110px">
                        <p style="font-size: 18px; font-weight: bold">
                          {{ book.bookname }}
                        </p>
                        <p style="font-size: 12px">{{ book.detail }}</p>
                      </v-col>
                      <v-col cols="12">
                        <v-row style="align-items: center">
                          <v-col cols="6">
                            <h4 style="">${{ book.price }}</h4>
                          </v-col>
                          <v-col cols="6" style="text-align: end">
                            <v-btn class="cart" icon @click="addBookToCart(book)"></v-btn>
                          </v-col>
                        </v-row>
                      </v-col>
                    </v-row>
                  </v-col>
                  <v-col cols="12">
                    <div>
                      <button
                        @click="changePage(currentPage - 1)"
                        :disabled="currentPage === 1"
                      >
                        Previous
                      </button>
                      <span>Page {{ currentPage }} of {{ totalPages }}</span>
                      <button
                        @click="changePage(currentPage + 1)"
                        :disabled="currentPage === totalPages"
                      >
                        Next
                      </button>
                    </div>
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
            <v-container>
              <v-alert class="alertDialog" v-if="isAlert" :type="typeAlert" closable>
                <strong>{{ titleAlert }}</strong>
              </v-alert>
            </v-container>
          </v-container>
          <Footer />
        </div>
      </v-main>
    </v-app>
  </div>
</template>

<script>
import CardItem from "@/components/CardItems.vue";
import NavBar from "@/components/NavBar.vue";
import SideBar from "@/components/SideBar.vue";

export default {
  name: "index",
  components: {
    NavBar,
  },
  data() {
    return {
      book: [],
      categories: [],
      cart: [],
      selectedCategories: "All",
      isAlert: false,
      titleAlert: "",
      typeAlert: "",
      currentPage: 1,
      itemLimit: 10,
    };
  },
  async fetch() {
    var url = "https://api.gameliveball.com/items";
    try {
      const books = await this.$axios.$get(url);
      const typeBook = this.getCategories(books.data);

      this.categories = typeBook;
      this.book = books.data;
    } catch (error) {
      console.log(error);
      this.book = [];
      this.categories = [];
    }
  },
  computed: {
    filteredBooks() {
      if (this.selectedCategories != "All") {
        return this.book.filter(
          (bookItem) => bookItem.category === this.selectedCategories
        );
      }
      return this.book;
    },
    totalItems() {
      return this.book.length;
    },
    totalPages() {
      return Math.ceil(this.totalItems / this.itemLimit);
    },
    paginatedBooks() {
      const start = (this.currentPage - 1) * this.itemLimit;
      const end = start + this.itemLimit;
      return this.book.slice(start, end);
    },
  },
  watch: {
    selectedCategories(newVal) {
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
    addBookToCart(book) {
      let bookFound = false;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i].id === book.id) {
          this.cart[i].count += 1;
          bookFound = true;
          break;
        }
      }

      if (!bookFound) {
        this.cart.push({ ...book, count: 1 });
      }
      console.log(this.cart);
    },
    showAlert() {
      this.titleAlert = "Add Book Successfully";
      this.isAlert = true;
      this.typeAlert = "success";
      setTimeout(() => {
        this.isAlert = false;
      }, 3000);
    },
    changePage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
      }
    },
    resetPagination() {
      this.currentPage = 1;
    },
  },
};
</script>

<style>
body {
  font-family: "Roboto", sans-serif;
}
.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  z-index: 1;
  transition: opacity 0.3s;
}

.v-card--reveal {
  align-items: center;
  bottom: 0;
  justify-content: center;
  opacity: 0.9;
  position: absolute;
  width: 100%;
}

.cart {
  background: url("assets/cart.png") no-repeat center center;
  background-size: contain;
}

.alertDialog {
  position: fixed;
  top: 80px;
  right: 20px;
  background-color: #28a745 !important;
}
</style>

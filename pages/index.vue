<template>
  <div data-app>
    <v-app>
      <v-main>
        <div>
          <NavBar :cart="cart" />
          <body>
            <v-container style="width: 100%">
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
                  <v-row style="align-items: center">
                    <h4 style="margin-right: 10px">List Show</h4>
                    <v-btn-toggle v-model="itemLimit" color="primary">
                      <v-btn :value="10">10</v-btn>
                      <v-btn :value="20">20</v-btn>
                      <v-btn :value="30">30</v-btn>
                      <v-btn :value="totalItems">All</v-btn>
                    </v-btn-toggle>
                  </v-row>
                  <v-col cols="12">
                    <p style="margin: 0">results: {{ filteredBooks.length }}</p>
                  </v-col>
                  <v-row>
                    <v-col cols="3" v-for="(book, index) in filteredBooks" :key="index">
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
                          <p style="font-size: 18px; font-weight: bold; margin: 0">
                            {{ book.bookname }}
                          </p>
                          <p style="font-size: 12px; margin: 0; height: 36px">
                            {{ book.detail }}
                          </p>
                          <p style="font-size: 10px; color: grey; margin-top: 10px">
                            Category: {{ book.category }}
                          </p>
                        </v-col>
                        <v-col cols="12" style="padding: 0 10px">
                          <v-row style="align-items: center">
                            <v-col cols="6">
                              <h4 style="">${{ book.price }}</h4>
                            </v-col>
                            <v-col cols="6" style="text-align: end">
                              <v-btn
                                class="cart"
                                icon
                                @click="addBookToCart(book)"
                              ></v-btn>
                            </v-col>
                          </v-row>
                        </v-col>
                      </v-row>
                    </v-col>
                    <v-col cols="12" style="margin-top: 20px">
                      <v-row v-if="filteredBooks.length">
                        <v-col cols="4" style="text-align: end">
                          <button
                            v-if="currentPage != 1"
                            @click="changePage(currentPage - 1)"
                            :disabled="currentPage === 1"
                          >
                            Previous
                          </button>
                        </v-col>
                        <v-col cols="4" style="text-align: center">
                          <span>Page {{ currentPage }} of {{ totalPages }}</span>
                        </v-col>
                        <v-col cols="4">
                          <button
                            v-if="currentPage != totalPages"
                            @click="changePage(currentPage + 1)"
                            :disabled="currentPage === totalPages"
                          >
                            Next
                          </button>
                        </v-col>
                      </v-row>
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
          </body>
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
  mounted() {
    this.book;
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
  watch: {
    itemLimit() {
      if (this.currentPage > this.totalPages) {
        this.currentPage = this.totalPages;
      }
    },
  },
  computed: {
    filteredBooks() {
      var dataFilter = this.book;
      if (this.selectedCategories != "All") {
        dataFilter = dataFilter.filter(
          (bookItem) => bookItem.category === this.selectedCategories
        );
      }
      const start = (this.currentPage - 1) * this.itemLimit;
      const end = start + this.itemLimit;
      return dataFilter.slice(start, end);
    },
    totalItems() {
      if (this.selectedCategories !== "All") {
        return this.book.filter(
          (bookItem) => bookItem.category === this.selectedCategories
        ).length;
      }
      return this.book.length;
    },
    totalPages() {
      console.log(this.totalItems);
      console.log(this.itemLimit);
      return Math.ceil(this.totalItems / this.itemLimit);
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
  },
};
</script>

<style>
body {
  font-family: "Roboto", sans-serif;
  width: 100vw;
  height: 100vh;
  margin: 0;
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

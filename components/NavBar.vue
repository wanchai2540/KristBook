<template>
  <nav>
    <v-row>
      <v-col cols="6">
        <img class="logo" src="logo.jpg" alt="" />
      </v-col>
      <v-col cols="6" style="display: flex; justify-content: end">
        <v-badge color="red" v-if="cart.length > 0" :content="cart.length" overlap>
          <v-avatar>
            <img @click="openModal" class="shopping-cart" src="shopping-bag.png" alt="" />
          </v-avatar>
        </v-badge>
        <v-avatar v-else>
          <img @click="openModal" class="shopping-cart" src="shopping-bag.png" alt="" />
        </v-avatar>
        <!-- Modal -->
        <v-dialog v-model="modalVisible" max-width="400px">
          <v-card v-if="cart.length > 0">
            <v-card-text>
              <v-row>
                <v-col cols="12">
                  <h4>You have {{ cart.length }} books in your cart</h4>
                </v-col>
                <!-- List Book -->
                <v-col cols="12" v-for="(data, index) in cart" :key="index">
                  <v-row cols="12">
                    <v-col cols="3" style="justify-content: center; display: flex">
                      <img class="imageBookModal" :src="data.photolink" />
                    </v-col>
                    <v-col cols="7">
                      <v-col cols="12">
                        <p style="font-size: 14px">{{ data.bookname }}</p>
                      </v-col>
                      <v-col cols="12">
                        <h4>{{ data.count }} x ${{ data.price }}</h4>
                      </v-col>
                      <v-col cols="12">
                        <p style="font-size: 14px">Category: {{ data.category }}</p>
                      </v-col>
                    </v-col>
                    <v-col cols="2" style="justify-content: center; display: flex">
                      <img
                        @click="removeBookById(index)"
                        class="deleteBtnModal"
                        src="recycling-bin.png"
                      />
                    </v-col>
                  </v-row>
                  <v-col cols="12" style="padding-left: 0; padding-right: 0">
                    <v-divider></v-divider>
                  </v-col>
                </v-col>
                <v-row cols="12">
                  <v-col cols="6"><h3>Total</h3></v-col>
                  <v-col cols="6" style="text-align: end"
                    ><h3>${{ sumTotal() }}</h3></v-col
                  >
                </v-row>
                <!-- List Book -->
              </v-row>
            </v-card-text>
            <v-card-actions>
              <v-col cols="12">
                <v-col cols="12" class="viewCartBtn">
                  <v-btn class="viewCartBtn" text @click="closeModal">View Cart</v-btn>
                </v-col>
                <v-col cols="12" class="checkoutBtn">
                  <v-btn class="checkoutBtn" text @click="closeModal">Checkout</v-btn>
                </v-col>
              </v-col>
            </v-card-actions>
          </v-card>
          <v-card v-else>
            <v-card-text>
              <v-col cols="12" style="display: flex; justify-content: center">
                <h3>No Books In Cart</h3>
              </v-col>
            </v-card-text>
            <v-card-actions>
              <v-col cols="12" class="closeModalBtn">
                <v-btn class="closeModalBtn" text @click="closeModal">Close</v-btn>
              </v-col>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-col>
    </v-row>
  </nav>
</template>

<script>
export default {
  name: "NuxtTutorial",
  data() {
    return {
      modalVisible: false,
      counterBadge: 1,
    };
  },
  props: {
    cart: {
      type: Array,
      required: true,
    },
  },
  methods: {
    openModal() {
      this.modalVisible = true;
    },
    closeModal() {
      this.modalVisible = false;
    },
    removeBookById(index) {
      this.cart.splice(index, 1);
    },
    sumTotal() {
      var total = this.cart.reduce((old, current) => {
        return old + current.price * current.count;
      }, 0);
      return total;
    },
  },
};
</script>

<style>
.logo {
  width: 10%;
}
.v-avatar{
  min-width: 80px !important;
}
.shopping-cart {
  width: 6%;
  object-fit: contain;
  margin-right: 1em;
}
.v-badge__wrapper {
  left: -30px !important;
  top: 10px !important;
}

.v-card__text {
  padding-top: 26px !important;
}

.imageBookModal {
  width: 100%;
  object-fit: contain;
}
.deleteBtnModal {
  width: 80%;
  object-fit: contain;
}
.viewCartBtn {
  display: flex;
  justify-content: center;
  border-radius: 8px;
  margin-bottom: 10px;
  padding: 0;
}
.viewCartBtn button {
  border: 1px solid black !important;
  border-radius: 8px !important;
  margin-bottom: 10px !important;
  height: 60px !important;
  width: 100% !important;
}

.checkoutBtn {
  display: flex;
  justify-content: center;
  background-color: black;
  border-radius: 8px !important;
  padding: 0;
}
.checkoutBtn button {
  color: white !important;
  width: 100% !important;
  height: 60px !important;
}
.closeModalBtn {
  display: flex;
  justify-content: center;
  background-color: black;
  border-radius: 8px !important;
  padding: 0;
}
.closeModalBtn button {
  color: white !important;
  width: 100% !important;
  height: 60px !important;
}
</style>

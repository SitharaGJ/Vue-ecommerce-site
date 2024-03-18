<template>
 <v-layout>
  <div class="home">
    <v-row>
    <v-col cols=""><img  src="../assets/img.png" style="height: 45px"></v-col>
    <v-col><h2 style="color: #cf3e2e">CEYLON.LK</h2></v-col>
    <v-col> <v-btn  color="#cf3e2e"  @click.stop="drawer = !drawer" >Cart Items: {{ CartList.length }}</v-btn></v-col>
    </v-row>


    <!-- //searchbar -->
    <!-- <v-row justify="center">
    <v-col cols="10">
      <v-autocomplete
    v-model="searchQuery"
    :items="productList"
    label="Search Products"
    variant="outlined"
    item-text="name"
    hide-no-data
  >
    <template v-slot:append>
      <v-btn icon @click="clearSearch">
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </template>
  </v-autocomplete>
    </v-col>
    </v-row> -->

    <v-snackbar
      v-model="snackbar"
      color="success"
      timeout="1000"
      location="top"
    >
    Item Successfully Added to Cart

      <template v-slot:actions>
        <v-btn
          
         
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>

    <v-divider class="mx-10 mt-5"></v-divider>

    <v-navigation-drawer v-model="drawer" location="right" temporary class="z-index:5">
      <h3>Cart List</h3>
      <v-divider class="mt-5"></v-divider>
        <v-list >
        <v-list-item  v-for="(item, index) in CartList" :key="index" class="mt-5">
          <v-img height="80" :src="item.image" class="mb-2"></v-img>
        <p>Item Name: {{ item.title }}</p>
        <!-- <p>Item Price:${{ item.price }}</p> -->
        <p>Item Count: {{ item.count }}</p>
        <p><b>Total Price: ${{ item.count*item.price }}</b></p>
        <v-btn @click="removeFromCart(item, index)" variant="outlined" size="small" class="mt-2 " color="red">remove</v-btn>
        <v-btn  class="mt-2 " color="green">Buy Now</v-btn>
        <v-divider class="mt-4"></v-divider>
        </v-list-item>
        </v-list>
       <!-- <v-btn>Pay Now</v-btn> -->
      </v-navigation-drawer> 


    <div class="mx-10">
    <v-row no-gutters class="justify-center">
      <v-col cols="4" v-for="(product, index) in productList" :key="index">
        <v-card :loading="loading" outlined class="mx-5 my-10 " >
          <v-img height="250" :src="product.image"></v-img>
          <v-row class="justify-center text-center mx-5 mt-3">
            <h3>{{ product.title }}</h3>
          </v-row>

          <v-card-text class="text-center mt-2">
          
    <v-row class="text-center justyfy-space-between mx-auto justify-center align-center mt-1"
              style="background-color: antiquewhite; width: 50%; height:30px ; border-radius: 5px">
            <div class="d-flex">
              <div class="grey--text ">
                <b>Rating:{{ product.rating.rate }}</b>
              </div>
              <div class="grey--text ms-4">
                Count:{{ product.rating.count }}
              </div>
            </div>
            

            </v-row>



            <div class="my-4 text-subtitle-1">
            <b>Price:${{ product.price }}</b>
            </div>

            <div>{{ product.description }}</div>
          </v-card-text>

          <v-divider class="mx-4"></v-divider>

          <v-card-title>Available Colours</v-card-title>

        <v-row class="text-center justify-center">
        
            <v-chip-group v-model="selection" active-class="deep-purple accent-4 white--text" column>
              <v-chip style="color: red;">R</v-chip>
              <v-chip style="color: Green;">G</v-chip>
              <v-chip style="color: blue;">B</v-chip>
              <v-chip style="color: rgb(209, 27, 215);">P</v-chip>
            </v-chip-group>
        

        <v-divider class="mx-4 my-2"></v-divider>
        </v-row>

          

          <v-row class="p-2">
          <div class="d-flex mx-5 mb-8 justify-center  align-center" >
            <v-btn  color="#C9C9C9"  variant="flat"  @click="decreaseCount(product.count, index)" class="ml-2">-</v-btn>  
            <p class="ml-2"> {{product.count }}</p>
            <v-btn  class="ml-2" variant="flat"  color="#99BAF7"  @click="increaseCount(product.count, index)">+</v-btn>
          </div>


          <v-btn  :disabled="product.count < 1" class="ml-8 " color="#F55845" @click="addToCart(product);  snackbar = true">Add To Cart</v-btn>
          </v-row>
        </v-card>
      </v-col>
    </v-row>
    </div>



  </div>

</v-layout>
</template>

<script>

export default {
  name: 'HomeView',
  data() {
    return {
      drawer: false,
      ApiData: {},
      productList: [],
      CartList: [],
      count:'0',
      loading: false,
      selection: 1,
      snackbar: false,
    }
  },
  mounted() {
    this.lordData();

  },
 watch: {
     group() {
        this.drawer = false
      },
  },


  methods: {

    lordData() {
      fetch('https://fakestoreapi.com/products')
        .then(res => res.json())
        .then(jason => {
          // this.productList = jason;
          let data = jason;
          let obj = {}
          for(let i = 0; i < data.length; i++) {
            obj = {
              id: data[i].id,
              title: data[i].title,
              image: data[i].image,
              description: data[i].description,
              price: data[i].price,
              rating: data[i].rating,
              category: data[i].category,
              count: 0,
            }
            this.productList.push(obj)
          }
          console.log(this.productList);
          console.log(this.productList[0].title);
        })

        .catch(error => {
          console.error('Error fetching products:', error); // Handle potential errors
        });
    },


    increaseCount(count, index) {
     this.productList[index].count++;
    },
    decreaseCount(count,index) {
      if (count > 0) {
        this.productList[index].count--;
      }
    },
    addToCart(product) {
      this.CartList.push(product);
      console.log(this.CartList);
    },

    removeFromCart(product) {
      let index = this.CartList.indexOf(product);
      if (index > -1) {
        this.CartList.splice(index, 1);
      }
    },

    goToCart() {
     
    }
  },



}
</script>

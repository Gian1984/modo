<!-- This example requires Tailwind CSS v2.0+ -->
<script setup>
import { SearchIcon, ChevronLeftIcon, ChevronRightIcon } from '@heroicons/vue/solid'
</script>

<template>
<section class="bg-grigio">
  <div class="px-10" style="height: 100vh">
    <div class="py-4">
      <div class="max-w-3xl mx-auto grid grid-cols-1 gap-6 sm:px-6 lg:max-w-full lg:grid-flow-col-dense lg:grid-cols-6">
        <div class="space-y-6 lg:col-start-1 lg:col-span-4">
          <div class="relative w-full text-gray-400 focus-within:text-gray-600">
            <div class="absolute inset-y-0 left-0 flex items-center pointer-events-none px-8 text-text">
              <SearchIcon class="h-5 w-5" aria-hidden="true" />
            </div>
            <input v-model="search" id="search-field" class=" block w-full h-full pl-20 pr-3 py-3 text-gray-900 placeholder-text focus:outline-none focus:placeholder-gray-400  sm:text-normal border border-bord rounded-full" placeholder="Search" type="search" name="search" />
          </div>
        </div>
        <div aria-labelledby="timeline-title" class="lg:col-start-5 lg:col-span-4">
          <div class="relative w-full text-gray-400 focus-within:text-gray-600">
            <button @click="findProduct"  class=" block w-full h-full pr-3 py-2.5 text-bianco bg-modo sm:text-normal rounded-full font-light">Find product</button>
          </div>
        </div>
      </div>
    </div>

    <div class="py-10">
      <div class="mt-8 max-w-3xl mx-auto grid grid-cols-1 gap-6 sm:px-6 lg:max-w-full lg:grid-flow-col-dense lg:grid-cols-4">
        <div class="space-y-6 lg:col-start-1 lg:col-span-3">
          <!-- product list-->
          <div v-if="products != ''">
            <div class="max-w-3xl mx-auto md:flex md:items-center md:justify-between md:space-x-5 lg:max-w-full">
              <div class="flex items-center space-x-5">
                <div>
                  <h1 class="text-3xl font-normal text-modo">{{products.length}} products found for {{search}}</h1>
                </div>
              </div>
            </div>
            <div v-for="(product, index) in products.slice(pageStart, pageStart + 10)" class="mt-3">
              <div aria-labelledby="applicant-information-title">
                <div class="bg-bianco sm:rounded-lg">
                  <div class="px-4 py-5 sm:px-6">
                    <div class="grid grid-cols-1 gap-x-4 sm:grid-cols-2">
                      <div class="sm:col-span-2">
                        <p class="text-sm font-medium text-gray-500 text-modo">{{product.brand}} {{product.title}}</p>
                      </div>
                      <div class="sm:col-span-2">
                        <p class="text-sm font-small text-modo">USD {{product.price}} <span class="text-span">- stock: {{product.stock}}</span></p>
                      </div>
                      <div class="sm:col-span-2">
                        <p class="mt-1 text-sm font-small text-text">{{product.description}}</p>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!--pagination section-->
            <div class="flex justify-center items-center mt-3">
              <div class="bg-gray-100 px-4 py-3 flex items-center justify-between sm:px-6">
                <div class="sm:flex-1 sm:flex sm:items-center sm:justify-between">
                  <div>
                    <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">
                      <button v-bind:class="{ disabled: currPage === 1 }" @click.prevent="setPage(currPage - 1)" class="relative inline-flex items-center px-2 py-2 rounded-l-md border text-sm font-medium bg-bianco border-bord text-modo hover:bg-modo hover:text-bianco">
                        <span class="sr-only">Previous</span>
                        <ChevronLeftIcon class="h-5 w-5" aria-hidden="true" />
                      </button>
                      <!-- Current: "z-10 bg-indigo-50 border-indigo-500 text-indigo-600", Default: "bg-white border-gray-300 text-gray-500 hover:bg-gray-50" -->
                      <button v-for="n in pages" @click.prevent="setPage(n)" class="bg-bianco border-bord text-modo hover:bg-modo hover:text-bianco relative inline-flex items-center px-4 py-2 border text-sm font-medium"> {{ n }} </button>
                      <button v-bind:class="{ disabled: currPage === pages }" @click.prevent="setPage(currPage + 1)" class="relative inline-flex items-center px-2 py-2 rounded-r-md border bg-bianco border-bord text-modo hover:bg-modo hover:text-bianco text-sm font-medium">
                        <span class="sr-only">Next</span>
                        <ChevronRightIcon class="h-5 w-5" aria-hidden="true" />
                      </button>
                    </nav>
                  </div>
                </div>
              </div>
            </div>
            <!--pagination section-->

          </div>
          <!-- product list-->

          <!--  spinner to indicate that a request is in progress -->
          <div v-if="loading" class="bg-white min-h-full px-4 py-16 sm:px-6 sm:py-24 md:grid md:place-items-center lg:px-8">
            <div class="max-w-max mx-auto">
              <main class="sm:flex">
                <div class="sm:ml-6">
                  <div class=" sm:pl-6">
                    <!--  spinner  -->
                    <div class="flex justify-center items-center">
                      <div
                          class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-modo"
                      ></div>
                      <!--  spinner  -->
                    </div>
                  </div>
                </div>
              </main>
            </div>
          </div>
          <!--  spinner to indicate that a request is in progress -->
        </div>

        <!--newsletter section-->
        <section aria-labelledby="timeline-title" class="lg:col-start-4 lg:col-span-1">
          <div class="bg-bianco px-4 py-5 sm:rounded-lg sm:px-6">
            <h1 id="timeline-title" class="text-lg font-normal text-xl text-modo">Notify me!</h1>
            <p class="text-sm text-text">
              Do you want to be update on new product based on your search? Provide an email address on witch you will be able to receive periodic emails. Don't miss this opportunity!
            </p>
            <div class="pt-5">
              <label for="email" class="block text-sm text-text">Email</label>
              <div class="mt-1">
                <input type="email" name="email" id="email" class=" block w-full sm:text-sm  rounded-md p-2 border border-bord" placeholder="Your address email here..." />
              </div>
            </div>
            <div class="mt-6 flex flex-col justify-stretch">
              <button type="button" class="inline-flex items-center justify-center px-4 py-3 border border-transparent text-normal font-light rounded-full shadow-sm text-bianco bg-modo">Continue</button>
            </div>
            <div class="mt-6 flow-root">
              <p class="text-xs text-span">
                By clicking "Notify me!", I gave Acme, Inc. consent to process my data and to send me email alerts, as detailed in Acme Inc.'s Privacy Policy. I May withdraw my consent or unsubscribe at any time.
              </p>
            </div>
          </div>
        </section>
        <!--newsletter section-->

      </div>
    </div>
  </div>
</section>

</template>

<script>

export default {

  data:() =>{
    return {
      perPage: 10,
      pages:'',
      currPage: 1,
      products:'',
      search:'',
      loading: false,
      filter_products: '',
      empty:''
    }
  },

  // mounted(){
  //   this.axios.get('https://dummyjson.com/products')
  //       .then(response => {
  //             console.log(response.data)
  //             console.log(this.search)
  //             this.products = response.data.products
  //         })
  // },

  methods:{

    findProduct(){
      this.loading = true
      let find = this.search
      console.log(find)
      this.axios.get('https://dummyjson.com/products/search?q='+find)
          .then(response => {
            this.products = response.data.products
            this.pages = Math.ceil(this.products.length / this.perPage)
            this.loading = false
          })
          .catch((error) => {
            this.loading = false
            this.empty = error.response.data.message;
          });
    },

    setPage: function(idx){
      if( idx <= 0 || idx > this.pages ){
        return;
      }
      console.log(idx)
      this.currPage = idx;
    },

  },

  computed:{
    pageStart: function(){
      return (this.currPage - 1) * this.pages;
    }
  }

}
</script>

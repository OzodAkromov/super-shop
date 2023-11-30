<template>
  <section class="about">
    <div class="container">
      <div class="header flex items-center justify-between mt-14 mb-5">
        <h1 class="text-[40px]">Edit page</h1>
        <TheButton @click="$router.push({ name: 'home' })">
          Home
          <i class="fa-solid fa-house"></i>
        </TheButton>
      </div>

      <div v-if="sent"
        class="w-[550px] mx-auto rounded-md shadow-lg flex flex-col items-center justify-center gap-5 p-5 py-10 ">
        <h1 class="font-mono text-xl">Product has been changed</h1>
        <TheButton @click="$router.push({ name: 'home' })">
          Go Home
          <i class="fa-solid fa-house"></i>
        </TheButton>
      </div>

      <form @submit.prevent v-else
        class="form w-[550px] mx-auto rounded-md shadow-lg flex flex-col items-start justify-center gap-5 p-5 py-10">
        <h2 class="font-mono text-xl">Product Information</h2>
        <!-- name input -->
        <input class="border w-full py-3 px-5 transition-all focus:ring-4" type="text" placeholder="Enter product name"
          v-model.trim="product.name"
          :class="[isTrue ? 'focus:outline-red-500 focus:ring-0' : 'focus:outline-purple-700 focus:ring-purple-500']">
        <!-- error text -->
        <p class="text-red-500 text-sm" v-if="errors.name">
          {{ errors.name }}
        </p>

        <!-- price input -->
        <input class="border w-full py-3 px-5 transition-all focus:ring-4" type="number" placeholder="Enter product price"
          v-model.trim="product.price"
          :class="[isTrue ? 'focus:outline-red-500 focus:ring-0' : 'focus:outline-purple-700 focus:ring-purple-500']">
        <!-- error text -->
        <p class="text-red-500 text-sm" v-if="errors.price">
          {{ errors.price }}
        </p>

        <!-- image input -->
        <input class="border w-full py-3 px-5 transition-all focus:ring-4" type="text"
          placeholder="Enter product image url" v-model="product.image"
          :class="[isTrue ? 'focus:outline-red-500 focus:ring-0' : 'focus:outline-purple-700 focus:ring-purple-500']">
        <!--  error text -->
        <p class="text-red-500 text-sm" v-if="errors.image">
          {{ errors.image }}
        </p>

        <button class="w-full py-3 px-5 text-white bg-purple-800" @click="editProduct">Edit</button>
      </form>

    </div>
  </section>
</template>

<script>
import TheButton from '../components/TheButton.vue';

export default {
  components: {
    TheButton
  },
  data() {
    return {
      API: 'https://super-shop-dfc4f-default-rtdb.firebaseio.com/products/',
      isTrue: true,
      productId: this.$route.params.id,
      product: {
        name: '',
        price: '',
        image: ''
      },
      errors: {
        name: '',
        price: '',
        image: ''
      },
      sent: false,
    }
  },
  watch: {
    product: {
      handler(newValue) {
        if (newValue.name.length < 4) {
          this.errors.name = "Product name can't be less than 4"
        }
        else if (newValue.name.length > 20) {
          this.errors.name = "Product name can't be more than 20"
        }
        else {
          this.isTrue = false
          this.errors.name = ""
        }

        if (newValue.price.length < 2) {
          this.errors.price = "Product price can't be less than 10 dollors"
        }
        else if (newValue.price.length > 5) {
          this.errors.price = "Product price can't be more than 10000 dollors"
        } else {
          this.isTrue = false
          this.errors.price = ""
        }

        const httpRegex = /^https?:\/\/(?:www\.)?[-a-zA-Z0-9@: %._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b(?:[-a-zA-Z0-9()@:%_\+.~#?&\/=]*)$/
        if (newValue.image.length < 1) {
          this.errors.image = "This field can not be empty"
        } else if (!httpRegex.test(newValue.image)) {
          this.errors.image = "Image url is not correct"
        } else {
          this.isTrue = false
          this.errors.image = ""
        }
      },
      deep: true
    },
  },
  methods: {
    editProduct() {
      if (this.product.name && this.product.price && this.product.image) {

        fetch(this.API + this.productId + '.json', {
          method: "PATCH",
          body: JSON.stringify(this.product),
          headers: {
            'Content-Type': 'application/json'
          }
        }).then((res) => {
          if (res.status === 200) {
            this.sent = !this.sent
            this.product.name = ""
            this.product.price = ""
            this.product.image = ""
          }
        })
      }
    },
    async getOneProduct() {
      const response = await fetch(this.API + this.productId + '.json')
      const data = await response.json()
      this.product.name = data.name
      this.product.price = data.price
      this.product.image = data.image
    },
  },
  mounted() {
    this.getOneProduct()
  }
}
</script>

<style scoped>
#test[type='file'] {
  display: none;
}

input[type="number"] {
  -webkit-appearance: textfield;
  -moz-appearance: textfield;
  appearance: textfield;
}

input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
}
</style>
<template>
  <section>
    <div class="container">
      <div class="header flex items-center justify-between mt-14 mb-5">
        <h1 class="text-[40px]">Products page</h1>
        <TheButton @click="$router.push({ name: 'create' })">
          Create
          <i class="fa-solid fa-plus"></i>
        </TheButton>
      </div>

      <div class="wrapper flex items-stretch justify-center flex-wrap gap-10">
        <div @click="openModal(id)" class="card w-[250px] p-3 shadow-lg rounded-lg cursor-pointer"
          v-for="(card, id) in products" :key="card">
          <img class="w-full rounded-md" :src="card.image" alt="#">

          <h3 class="title text-lg font-semibold mt-5">
            {{ card.name }}
          </h3>

          <div class="flex items-center justify-between">

            <p class="text text-right text-2xl font-semibold my-5">
              {{ card.price + '$' }}
            </p>

            <TheButton>
              <i class="fa-solid fa-cart-shopping"></i>
              Buy
            </TheButton>
          </div>
        </div>
      </div>
    </div>
  </section>

  <div @click="closeModal" class="modal-wrapper" v-if="isModalOpened">
    <TheModal :productId="productId" @action="closeModal"/>
  </div>
</template>

<script>
import TheButton from '../components/TheButton.vue';
import TheModal from '../components/TheModal.vue';

export default {
  components: {
    TheButton,
    TheModal
  },
  data() {
    return {
      API: 'https://super-shop-dfc4f-default-rtdb.firebaseio.com/products.json',
      products: null,
      isModalOpened: false,
      modalInfo: '',
      productId: ''
    }
  },
  methods: {
    async getProducts() {
      const response = await fetch(this.API)
      const data = await response.json()
      this.products = data
    },
    openModal(id) {
      this.isModalOpened = true
      this.productId = id
    },
    closeModal() {
      this.isModalOpened = false
    }
  },
  mounted() {
    this.getProducts()
  }
}
</script>

<style lang="scss" scoped>

.modal-wrapper {
  background: rgba(0, 0, 0, 0.550);
  position: fixed;
  left: 0;
  top: 0;
  z-index: 99;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
//  cursor: pointer;
}
</style>
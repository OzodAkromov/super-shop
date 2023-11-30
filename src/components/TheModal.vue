<template>
    <div class="modal w-auto h-auto bg-white flex items-start gap-12 p-6 rounded-2xl mx-auto relative"
        @click.stop>
        <div @click="$emit('action')" class="icon cursor-pointer w-10 h-10 bg-white absolute right-[-15px] top-[-15px] flex items-center justify-center text-black text-3xl rounded-xl shadow-xl">
            <i class="fa-solid fa-xmark"></i>
        </div>
        <div class="modal-content">
            <div class="image-block w-80 h-full">
                <img :src="modalInfo.image" alt="#" class="w-full h-full object-cover">
            </div>
            <div class="text-content mt-10">
                <h3 class="text-4xl my-3">{{ modalInfo.name }}</h3>
                <p>{{ modalInfo.price }}$</p>
            </div>
            <div class="btns flex items-center justify-center gap-5 mt-5">
                <button @click="editProduct"
                    class="btn px-5 py-3 text-white text-2xl text-center bg-yellow-500 rounded-lg flex-grow">
                    Edit
                </button>
                <button @click="deleteProduct"
                    class="btn px-5 py-3 text-white text-2xl text-center bg-red-500 rounded-lg flex-grow">
                    Delete
                </button>
            </div>
        </div>
    </div>
</template>
  
<script>
export default {
    props: {
        productId: {
            type: [String, Object],
            required: true
        }
    },
    data() {
        return {
            API: `https://super-shop-dfc4f-default-rtdb.firebaseio.com/products/`,
            modalInfo: '',
        }
    },
    methods: {
        async getOneProduct() {
            const response = await fetch(this.API + this.productId + '.json')
            const data = await response.json()
            this.modalInfo = data
        },
        deleteProduct() {
            let answer = prompt(`Enter product name to delete: ${this.modalInfo.name}`)
            if (answer === this.modalInfo.name) {
                fetch(this.API + this.productId + '.json', {
                    method: 'DELETE',
                })
                    .then(res => {
                        if (res.status === 200) {
                            this.$emit('action')
                            alert("Product has been deleted!")
                        }

                    })
            } else {
                alert('You entered wrong name!')
            }
        },
        editProduct() {
            this.$router.push({name: 'edit', params: {id: this.productId}})
        }
    },
    mounted() {
        this.getOneProduct()
    }
}
</script>
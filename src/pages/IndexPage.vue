<template>
  <q-page class="flex column flex-center">
    <q-card class="my-card" >
      <img :src="product.thumbnail">

      <q-card-section>
        <div class="text-h6">{{ product.title }}</div>
        <div class="text-subtitle2">{{ product.price }} EUR</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
        {{ product.description }}
      </q-card-section>
    </q-card>

    <q-btn @click="selected += 1">
      Next
    </q-btn>

    <q-btn @click="selected -= 1">
      Previous
    </q-btn>


  </q-page>
</template>

<script>
import { computed, defineComponent } from 'vue'
import { useStore } from 'vuex'
import axios from 'axios';

export default defineComponent({
  name: 'IndexPage',
  async preFetch({ store }) {
    try {
      const response = await axios('https://dummyjson.com/products');
      store.commit('setProducts', response.data);
    } catch (e) {
      console.error(e)
      return
    }
  },
  setup () {
    const $store = useStore();

    // display the item from store state.
    const products = computed(() => $store.state.products.products);

    return { products }
  },
  data() {
    return {
      selected: 0,
    }
  },
  computed: {
    product() {
      return this.products[this.selected];
    },
  }
})
</script>

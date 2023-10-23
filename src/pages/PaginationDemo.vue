<template>
    <div>
        <h1>Infinite and virtual scroll</h1>
        <div id="pagination-demo-scroll" style="height: 200px; overflow: scroll; background-color: pink;">
          <q-infinite-scroll @load="load" :offset="50" scroll-target="#pagination-demo-scroll">
              <div v-for="(product, i) in products" :key="product.id">
                  {{ i + 1 }}. {{ product.title }}
              </div>
              <template v-slot:loading> loading... </template>
          </q-infinite-scroll>

          <q-btn :to="nextRoute" class="hidden">Next page</q-btn>
        </div>
    </div>
</template>

<script>
import { defineComponent } from 'vue';
import { mapMutations, mapState } from 'vuex';
import axios from 'axios';

const pageSize = 15;

async function getProducts(params) {
  return axios.get('https://dummyjson.com/products', { params });
}

export default defineComponent({
    name: 'PaginationDemo',
    data() {
        return {
          initialLoad: true,
          currentPage: 1,
        };
    },
    computed: {
      ...mapState(['products']),
        nextRoute() {
            return { name: 'pagination-demo', query: { page: this.currentPage + 1 } };
        },
    },
    methods: {
        ...mapMutations(['setProducts']),
        async load(index, done) {
          if (this.initialLoad) {
            this.initialLoad = false;
            done();
            return;
          }
          try {
            const nextPage = this.currentPage + 1;
            this.currentPage = nextPage;
            const response = await getProducts({ limit: pageSize, skip: pageSize * (nextPage - 1 )});
            this.setProducts(response.data.products);
          } catch (e) {
            console.error(e)
          } finally {
            done();
          }
        },
    },
    async preFetch({ store, currentRoute }) {
      try {
          const page = Number(currentRoute.query?.page ?? 1);
          const response = await getProducts({ limit: pageSize * page });
          store.commit('setProducts', response.data.products);
        } catch (e) {
          console.error(e)
          return
        }
    },
});
</script>

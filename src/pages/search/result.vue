<template>
  <div class="result">
    <div class="loading-container" v-show="loading">
      <me-loading/>
    </div>
    <ul class="g-list" v-show="!loading && results.length">
      <li
        class="g-list-item"
        v-for="(item, index) in results"
        :key="index"
        @click="$_selectItem(item[0])"
      >
        <span class="g-list-text">{{item[0]}}</span>
      </li>
    </ul>
    <div class="no-result" v-show="!loading && !results.length">没有结果</div>
  </div>
</template>

<script>
  import MeLoading from 'base/loading';
  import {getSearchResult} from 'api/search';
  import {searchMixin} from 'assets/js/mixins';

  export default {
    name: 'SearchResult',

    data() {
      return {
        results: [],
        loading: false
      };
    },

    props: {
      query: {
        type: String,
        default: ''
      }
    },

    watch: {
      query(query) {
        this.getResults(query);
      }
    },

    methods: {
      getResults(keyword) {
        if(!keyword) return;
        this.loading = true;
        getSearchResult(keyword).then(data => {
          if (data) {
            this.results = data;
            this.loading = false;
          }
        });
      }
    },

    mixins: [searchMixin],

    components: {
      MeLoading
    }
  };
</script>

<style lang="scss" scoped>
  .loading-container {
    margin-top: 50px;
  }
</style>

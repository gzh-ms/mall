<template>
  <div class="hot">
    <h4 class="hot-title">热门搜索</h4>
    <div class="loading-container" v-if="!hots.length">
      <me-loading/>
    </div>
    <ul class="hot-list" v-else>
      <li
        class="hot-item"
        v-for="(item, index) in hots"
        :key="index"
        @click="$_selectItem(item.hotWord)"
      >{{item.hotWord}}</li>
    </ul>
  </div>
</template>

<script>
  import MeLoading from 'base/loading';
  import {getSearchHotKeyword} from 'api/search';
  import {searchMixin} from 'assets/js/mixins';

  export default {
    name: 'SearchHot',

    data() {
      return {
        hots: []
      };
    },

    mixins: [searchMixin],

    components: {
      MeLoading
    },

    created() {
      this.getHotKeyword().then(() => {
        this.$emit('loaded');
      });      
    },

    methods: {
      getHotKeyword() {
        return getSearchHotKeyword().then(data => {
          return new Promise(resolve => {
            if (data) {
              this.hots = data;
              resolve();
            }
          });
        });
      }
    }
  };
</script>

<style lang="scss" scoped>
  @import '~assets/scss/mixins';

  .hot {
    padding: 0 10px;
    background-color: #fff;
    border-bottom: 1px solid $border-color;
    margin-bottom: 10px;

    &-title {
      height: 34px;
      line-height: 34px;
      font-size: $font-size-l;
      font-weight: bold;
    }

    &-list {
      display: flex;
      flex-wrap: wrap;
    }

    &-item {
      padding: 8px;
      margin: 0 10px 10px 0;
      color: #686868;
      border-radius: 4px;
      background-color: #f0f2f5;
    }
  }

  .loading-container {
    padding: 10px 0;
  }
</style>

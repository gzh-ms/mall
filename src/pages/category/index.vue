<template>
  <div class="category">
    <header class="g-header-container">
      <category-header/>
    </header>
    <div class="g-content-container">
      <div class="sidebar">
        <category-tab @switch-tab="getCurrentId"/>
      </div>
      <div class="main">
        <category-content :curId="curId"/>
      </div>
    </div>
  </div>
</template>

<script>
  import CategoryHeader from "./header";
  import CategoryTab from "./tab";
  import CategoryContent from "./content";

  export default {
    name: 'Category',

    data() {
      return {
        curId: ''
      };
    },

    components: {
      CategoryHeader,
      CategoryTab,
      CategoryContent
    },

    methods: {
      getCurrentId(id) {
        this.curId = id;
      }
    }
  };
</script>

<style scoped lang="scss">
  @import '~assets/scss/mixins';

  .category {
    overflow: hidden;
    width: 100%;
    height: 100%;
    background-color: $bgc-theme;
  }
  
  .g-content-container {
    display: flex;
  }

  .sidebar {
    width: 80px;
    height: 100%;
  }

  .main {
    flex: 1;
    height: 100%;
  }
</style>

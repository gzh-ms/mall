<template>
  <div class="content-wrapper">
    <div class="loading-container" v-if="isLoading">
      <!-- <me-loading/> -->
      <div class="loading-wrapper">
        <me-loading/>
      </div>
    </div>
    <me-scroll ref="scroll" @scroll-end="scrollEnd">
      <div class="content">
        <div class="pic" v-if="content.banner">
          <a :href="content.banner.linkUrl" class="pic-link">
            <img
              :src="content.banner.picUrl"
              @load="updateScroll"
              class="pic-img"
            >
          </a>
        </div>
        <div class="section"
          v-for="(section, index) in content.data"
          :key="index"
        >
          <h4 class="section-title">{{section.name}}</h4>
          <ul class="section-list">
            <li
              class="section-item"
              v-for="(item, i) in section.itemList"
              :key="i"
            >
              <a :href="item.linkUrl" class="section-link">
                <p class="section-pic" v-if="item.picUrl">
                  <img v-lazy="item.picUrl" class="section-img"/>
                </p>
                <p class="section-name">{{item.name}}</p>
              </a>
            </li>
          </ul>
        </div>
      </div>
    </me-scroll>
    <div class="g-backtop-container">
      <me-backtop @backtop="backToTop" :visible="isBacktopVisible"/>
    </div>
  </div>
</template>

<script>
  import MeLoading from 'base/loading';
  import MeScroll from 'base/scroll';
  import MeBacktop from 'base/backtop';
  import {getCategoryContent} from "api/category";
  import storage from "assets/js/storage";

  import {CATEGORY_CONTENT_KEY, CATEGORY_CONTENT_UPDATE_TIME_INTERVAL} from "./config";

  export default {
    name: 'CategoryContent',

    data() {
      return {
        content: {},
        isBacktopVisible: false,
        isLoading: false
      };
    },

    props: {
      curId: {
        type: String,
        default: ''
      }
    },

    watch: {
      curId(id) {
        this.isLoading = true;
        this.getConent(id).then(() => {
          this.isLoading = false;
          this.backToTop(0);
        });
      }
    },

    methods: {
      getConent(id) {
        let contents = storage.get(CATEGORY_CONTENT_KEY);
        let updateTime;
        const curTime = new Date().getTime();

        if (contents && contents[id]) {
          updateTime = contents[id].updateTime || 0;
          if (curTime - updateTime <= CATEGORY_CONTENT_UPDATE_TIME_INTERVAL) { // LocalStorage
            return this.getContentByLocalStorage(contents[id]);
          } else { // http
            return this.getContentByHTTP(id).then(() => {
              this.updateLoacalStorage(contents, id, curTime);
            });
          } 
        } else {
          return this.getContentByHTTP(id).then(() => {
            this.updateLoacalStorage(contents, id, curTime);
          });
        }
      },

      getContentByLocalStorage(content) {
        console.log(content.data);
        this.content = content.data;
        return Promise.resolve();
      },

      getContentByHTTP(id) {
        return getCategoryContent(id).then(data => {
          return new Promise(resolve => {
            if (data) {
              this.content = data;
              resolve();
            }
          });
        });
      },

      updateLoacalStorage(contents={}, id, curTime) {
        contents[id] = {};
        contents[id].data = this.content;
        contents[id].updateTime = curTime;
        storage.set(CATEGORY_CONTENT_KEY, contents);
      },

      backToTop(speed) {
        this.$refs.scroll && this.$refs.scroll.scrollToTop(speed);
      },

      updateScroll() {
        this.$refs.scroll && this.$refs.scroll.update();
      },

      scrollEnd(translate, scroll) {
        this.isBacktopVisible = translate < 0 && -translate > scroll.height;
      }
    },

    components: {
      MeLoading,
      MeScroll,
      MeBacktop
    }
  };
</script>

<style lang="scss" scoped>
  @import "~assets/scss/mixins";

  .content-wrapper {
    position: relative;
    height: 100%;
  }

  .loading-container {
    position: absolute;
    top: 0;
    left: 0;
    z-index: $category-popup-z-index;
    @include flex-center();
    width: 100%;
    height: 100%;
    // background-color: $modal-bgc;

    .mine-loading {
      color: #fff;
      font-size: 14px;
    }
  }
  .loading-wrapper {
    width: 50%;
    padding: 30px 0;
    background-color: $modal-bgc;
    border-radius: 4px;
  }

  .content {
    padding: 10px;
  }

  .pic {
    margin-bottom: 12px;

    &-link {
      display: block;
    }

    &-img {
      width: 100%;
    }
  }

  .section {
    margin-bottom: 12px;

    &:last-child {
      margin-bottom: 0;
    }

    &-title {
      height: 28px;
      line-height: 28px;
      color: #080808;
      font-weight: bold;
    }

    &-list {
      display: flex;
      flex-wrap: wrap;
      background-color: #fff;
      padding: 10px 10px 0;
    }

    &-item {
      width: (100% / 3);
    }

    &-link {
      display: block;
    }

    &-pic {
      position: relative;
      width: 80%;
      padding-bottom: 80%;
      margin: 0 auto;
    }

    &-img {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }

    &-name {
      height: 36px;
      line-height: 36px;
      text-align: center;
      @include ellipsis();
    }
  }

  .g-backtop-container {
    bottom: 10px;
  }
</style>

<template>
  <div id="app">
    <div class="header">
      <span>Gists</span>
      <input type="datetime-local" v-model="date" />
    </div>
    <div class="holder" ref="scrollList">
      <div class="wrapper_img_center">
        <transition name="fade">
          <div v-if="current_user_img" class="img_center">
            <img :src="current_user_img" alt="user_img" />
          </div>
        </transition>
      </div>
      <div
        v-for="(item, index) in list"
        :key="index"
        class="item"
        @click="changeColor(index)"
        :class="{ selected: item.selected }"
      >
        <div class="wrapper_img">
          <img :src="item.owner.avatar_url" alt="img_avatar" />
          <div v-if="item.selected" class="img_ovelay"></div>
        </div>

        <p>{{ Object.keys(item.files)[0] }}</p>
      </div>
      <div v-if="loading">
        <TheLoader />
      </div>
    </div>

    <Pagination
      @sendPage="sendPage"
      :num_of_pages="num_of_pages"
      :current_page="current_page"
    />
  </div>
</template>
<script>
import axios from "axios";
import Pagination from "@/components/Pagination.vue";
import TheLoader from "@/components/TheLoader.vue";

export default {
  components: {
    Pagination,
    TheLoader,
  },
  data() {
    return {
      list: [],
      loading: false,
      num_of_pages: 2,
      current_page: 1,
      date: "",
      current_user_img: null,
      activeTimeout: null,
    };
  },

  mounted() {
    this.sendRequest();
  },
  watch: {
    date: {
      handler() {
        this.sendRequest();
      },
    },
  },
  methods: {
    async sendRequest() {
      this.loading = true;
      this.list = [];
      let url = `https://api.github.com/gists/public?page=${this.current_page}&per_page=30`;
      if (this.date != "") {
        url = `https://api.github.com/gists/public?page=${this.current_page}&per_page=30&since=${this.date}`;
      }
      await axios.get(url).then((response) => {
        if (response.data.length < 30) {
          this.num_of_pages = 1;
        }
        this.list = response.data;
        this.list.forEach((el) => {
          el.selected = false;
        });
        this.loading = false;
        this.$refs.scrollList.scrollTop = 0;
      });
    },
    sendPage(page) {
      this.current_page = page;
      this.sendRequest();
    },
    changeColor(index) {
      for (let i = 0; i < this.list.length; i++) {
        this.list[i].selected = false;
      }
      const copyArray = this.list[index];
      copyArray.selected = true;
      this.list.splice(index, 1, copyArray);
      this.showCenterImg(index);
    },
    showCenterImg(index) {
      this.current_user_img = this.list[index].owner.avatar_url;
      if (this.activeTimeout) {
        clearTimeout(this.activeTimeout);
        this.activeTimeout = null;
      }
      this.activeTimeout = setTimeout(() => {
        this.current_user_img = null;
      }, 1000);
    },
  },
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #303133;
  width: 1230px;
  margin: 0 auto;
  min-height: 100%;
}
body {
  background: #e5e5e5;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 50px 0;
}

.header {
  display: flex;
  justify-content: space-between;
  padding: 5px 16px;
  background-color: #f3f3f3;
  text-align: left;
  align-items: center;
  color: #000000;
  font-weight: bold;
  font-size: 15px;
  line-height: 17.25px;
}
.holder {
  position: relative;
  word-break: break-all;
  margin: 0 auto;
  background: white;
  height: 672px;
  padding: 0 16px;
  overflow-y: scroll;
  scroll-behavior: smooth;
}
.selected {
  color: #06a9f6;
}

.holder .wrapper_img {
  position: relative;
}
.wrapper_img {
  position: relative;
}
.img_ovelay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  opacity: 0.5;
  background: #303133;
}
.item {
  display: flex;
  align-items: center;
  padding: 12px 0px 12px 0px;
  border-bottom: 1px solid #eaeaea;
}
.item:hover {
  cursor: pointer;
}
.item p {
  margin-left: 16px;
}
.item img {
  width: 74px;
  height: 74px;
}
.footer {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  height: 40px;
  width: 100%;
  background-color: white;
  padding: 0 16px;
  gap: 5px;
}
.wrapper_img_center {
  position: sticky;
  top: 50%;
}
.img_center {
  height: 300px;
  position: absolute;
  top: calc(50% - 150px);
  left: calc(50% - 150px);
  width: 300px;
  pointer-events: none;
}
.img_center img {
  width: 100%;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
::-webkit-scrollbar {
  width: 14px;
}

::-webkit-scrollbar-track {
  background-color: #eaeaea;
}

::-webkit-scrollbar-thumb {
  height: 387px;
  background: #d8d8d8;

  border: 4px solid transparent;
  border-radius: 6.5px;
  background-clip: content-box;
}
</style>

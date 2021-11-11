<template>
  <div class="footer">
    <div v-for="page in num_of_pages" :key="page">
      <p :class="{ active: page == current_page }">{{ page }}</p>
    </div>
    <div class="arrows">
      <span
        :class="{ disable_Back_Arrow: current_page != num_of_pages }"
        @click="sendPage(current_page - 1)"
        class="arrow"
        ><img src="../images/nextArrow.svg" alt="backArrow"
      /></span>
      <span
        :class="{ disable_Next_Arrow: current_page == num_of_pages }"
        @click="sendPage(current_page + 1)"
        class="arrow"
        ><img src="../images/nextArrow.svg" alt="nextArrow"
      /></span>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    num_of_pages: Number,
    current_page: Number,
  },
  methods: {
    sendPage(page) {
      if (page == 0 || page > this.num_of_pages) return;
      this.$emit("sendPage", page);
    },
  },
};
</script>
<style scoped>
.footer .arrow {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  border-radius: 3px;
  /*padding: 6px 0 8px;*/
  margin-right: 6px;
  border: 1px solid #e5e5e5;
  /*background: white;*/
}
.arrow:nth-child(1) {
  transform: rotate(180deg);
}
.arrows {
  display: flex;
  margin-left: 8px;
  cursor: pointer;
}
.disable_Next_Arrow,
.disable_Back_Arrow {
  pointer-events: none;
  opacity: 0.4;
}
.footer .arrow img {
  width: 8px;
  height: 12px;
}
.active {
  color: #06a9f6;
}
</style>

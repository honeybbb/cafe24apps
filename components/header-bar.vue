<template>
  <header id="header">
    <div class="wrapper">
      <h1 class="logo">리뉴웨이브 배너 매니저</h1>
      <div class="right">
        <span class="user">리뉴웨이브</span>
        <a class="used" @click="openDialog">사용방법</a>
      </div>
    </div>

    <DialogManual :dialog="dialog" @close-dialog="closeDialog"/>
  </header>
</template>
<script>
import DialogManual from "./dialogManual"
export default {
  components: {
    DialogManual
  },
  data () {
    return {
      dialog: false
    }
  },
  methods: {
    openDialog() {
      this.dialog = true;
    },
    closeDialog() {
      this.dialog = false;
    },
    getMallId () {
      const urlParams = new URLSearchParams(window.location.search);
      const mallId = urlParams.get('mall_id'); // mall_id 값을 가져옴

      // mall_id가 존재할 경우 localStorage에 저장
      if (mallId) {
        localStorage.setItem('mall_id', mallId);
      } else {
        localStorage.getItem('mall_id');
      }

      const skinArr = [];

      history.replaceState({}, null, location.pathname);
    }
  },
  mounted() {
    this.getMallId()
  }
}
</script>

<template>
  <div id="app">
    <top-nav></top-nav>
    <router-view></router-view>
    <footer-nav></footer-nav>
    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import TopNav from "./components/TopNav";
import FooterNav from "./components/Footer";
import "bootstrap/dist/css/bootstrap.min.css";
import "bootstrap/dist/js/bootstrap.min.js";
import chainOpt from "@/utils/web3/chainOperation";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

window.allPoolInfo = []

export default {
  name: 'app',
  components: {
    FooterNav,
    TopNav,
    Loading
  },
  data() {
    return {
      loading: true,
    }
  },

  mounted() {
    setTimeout(_=>{
      chainOpt.wallet.loadWallet()
      this.loading = false
    }, 1000)
  }
}
</script>

<style lang="scss">
</style>

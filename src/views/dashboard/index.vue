<template>
  <div class="dashboard-container">
    <div class="row">
      <div class="col text-center">
        <img class="home-logo" :src="'/static/logo/logo.png'" alt=""/>
      </div>
    </div>
    <div class="row">
      <div class="col text-center welcome-tit">{{ $t("common.welcome") }}</div>
    </div>
    <div class="row">
      <div class="col text-center welcome-txt">{{ $t("common.welcomeTxt") }}</div>
    </div>

    <div class="row">
      <div class="col-xl-4 col-lg-4 col-md-4 col-sm-12 col-xs-12 text-center"
           v-for="(product, index) in products" :key="index">
        <product :product-data="product" :product-index="LPTokenToPoolID(product.LPToken)" :pool-enabled="product.stat"></product>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import Product from "./Product";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import {LPTokenToPoolID} from "@/utils/web3/constant";

let defaultChainInfo = {
  supply: 0,

  userStaked: 0,
  poolStaked: 0,

  rewardPerHour: 0,
  rewardPerDay: 0,
  rewardPerWeek: 0,
}

export default {
  name: "dashboard",
  components: { Product, Loading },

  data() {
    return {
      allBalance: null,
      poolInfo: null,
      loading: false,

      products: [
        {
          img: "/static/icon/TEFI.png",
          name: "TEFI, Double!",
          txt: "TEFI",
          tx: "TEFI",
          lp: false,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "9600" ,
          stat: false,
          dateTime: "2020-09-23 20:00:00",
          double: false,
          LPToken: "TSS89kdrcekgmErvaFv73raFiQePwnun8Z",
          link: "https://justswap.io/#/home?tokenAddress=TSS89kdrcekgmErvaFv73raFiQePwnun8Z&type=swap",
        },
        {
          img: "/static/icon/USDT.png",
          name: "USDT to SSO",
          txt: "USDT",
          tx: "USDT",
          lp: false,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "1600" ,
          stat: false,
          dateTime: "2020-09-23 20:00:00",
          double: false,
          LPToken: "TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t",
          link: "https://justswap.io/#/home?tokenAddress=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&type=swap",
        },
        {
          img: "/static/icon/TEFI-TRX.png",
          name: "TEFI TRX JLP",
          txt: "TEFI-TRX",
          tx: "TEFI-TRX",
          lp: true,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "4400" ,
          stat: false,
          dateTime: "2020-09-23 20:00:00",
          double: false,
          LPToken: "TVbTu1dVwwcJ4zVNwKdU98aSWkxHWD94jb",
          link: "https://justswap.io/#/home?tokenAddress=TSS89kdrcekgmErvaFv73raFiQePwnun8Z&type=add",
        },
        {
          img: "/static/icon/SSO-TRX.png",
          name: "Sirius Oracle!",
          txt: "SSO-TRX JLP",
          tx: "SSO-TRX",
          lp: true,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "6000" ,
          stat: false,
          dateTime: "2020-09-24 00:00:00",
          double: false,
          LPToken: "",
          link: "https://justswap.io/#/home",
        },
        {
          img: "/static/icon/SLM.png",
          name: "Salmon!",
          txt: "SLM",
          tx: "SLM",
          lp: false,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "800" ,
          stat: false,
          dateTime: "2020-09-23 20:00:00",
          double: false,
          LPToken: "TEbKj6dFUSs6Q6dW5F9R4DZwTWKV9EV2BR",
          link: "https://justswap.io/#/home?tokenAddress=TEbKj6dFUSs6Q6dW5F9R4DZwTWKV9EV2BR&type=swap",
        },
        {
          img: "/static/icon/PEARL.png",
          name: "Pearl!",
          txt: "Pearl",
          tx: "PEARL",
          lp: false,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "800" ,
          stat: false,
          dateTime: "2020-09-23 20:00:00",
          double: false,
          LPToken: "TGbu32VEGpS4kDmjrmn5ZZJgUyHQiaweoq",
          link: "https://justswap.io/#/home?tokenAddress=TGbu32VEGpS4kDmjrmn5ZZJgUyHQiaweoq&type=swap",
        },
        {
          img: "/static/icon/TRX.png",
          name: "Tron! TRX!",
          txt: "TRX",
          tx: "TRX",
          lp: false,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "400" ,
          stat: false,
          dateTime: "2020-09-30 20:00:00",
          double: true,
          LPToken: "TRX",
          link: "https://justswap.io/#/home",
        },
        {
          img: "/static/icon/SSO.png",
          name: "SSO,SSO!",
          txt: "SSO",
          tx: "SSO",
          lp: false,
          apy: "3000",
          chainInfo: defaultChainInfo,
          totalSupply: "400" ,
          stat: false,
          dateTime: "2020-09-30 20:00:00",
          double: true,
          LPToken: "TYoZt51oT8ceAZ9iuX6i4vZLY2KJvc4myF",
          link: "https://justswap.io/#/home",
        },
      ]
    }
  },

  methods: {
    LPTokenToPoolID: LPTokenToPoolID,
    seeTheMenu() {
      this.$router.push({name: "Dashboard"})
    },
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.dashboard-container {
  width: 100%;
  height: 100%;

  .home-logo{
    width: 120px;
    height: 120px;
    margin-bottom: 15px;
  }

  .welcome-tit {
    font-size: 27px;
    color: $fontColor;
    letter-spacing: 2px;
    font-family: $fontTit;
  }
  .welcome-txt {
    margin-bottom: 30px !important;
    color: $txtColor;
    font-family: $fontTxt;
  }
}
</style>

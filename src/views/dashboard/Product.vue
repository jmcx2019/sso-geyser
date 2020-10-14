<template>
  <div class="product-div">
    <div class="row">
      <div class="col">
        <div class="product-logo-div">
          <img class="product-logo" :src="productData.img" alt=""/>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <p class="pdt-name">{{ productData.name }}</p>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="pdt-txt" v-show="productData.lp">
          {{ $t("dashboard.product.deposit1") + productData.tx + $t("dashboard.product.deposit2")}}
        </div>
        <div class="pdt-txt" v-show="!productData.lp">
          {{ $t("dashboard.product.deposit") + productData.tx + $t("dashboard.product.uni")}}
        </div>
        <p class="pdt-txt">{{ $t("dashboard.product.earn") + $t("token.name") }}</p>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <countdown :dateTime="productData.dateTime"></countdown>
      </div>
    </div>
    <div class="row">
      <div class="col-6 col-btn-l">
        <input type="submit" class="btn btn-info btn-select" :value="$t('dashboard.product.goToBuy')"
               @click="goToBuy(productData.link)"
        >
      </div>
      <div class="col-6 col-btn-r">
        <input type="submit" class="btn btn-select"
               v-show="!productData.stat" :value="$t('date.unOpen')"
               disabled>
        <input type="submit" class="btn btn-select"
               v-show="productData.stat" :value="$t('dashboard.product.select')"
               @click="select"
        >
      </div>
    </div>
    <div class="row pdt-apy">
      <div class="col text-left">
        {{ $t("common.apy") }}
        <div></div>
      </div>
      <div class="col text-right">
        <div>{{ productData.apy }}%</div>
      </div>
    </div>
  </div>
</template>

<script>
import Countdown from "../../components/Countdown";
import chainOpt from "@/utils/web3/chainOperation";
import $ from "jquery";

let opt = chainOpt.opt

export default {
  name: "Product",
  components: { Countdown },
  props: ["productData", "productIndex", "poolEnabled"],
  data() {
    return {
      querying: false,
      poolInfo: null,
    }
  },
  mounted() {
    if(this.poolEnabled) {
      this.loopGetInfo()
      setInterval(async _=> {
        await this.loopGetInfo()
      }, 3000)
    }

    if (this.productData.stat) {
      $(this.$el).find("#selectBtn").removeAttr("disabled")
    } else {
      $(this.$el).find("#selectBtn").attr("disabled", "disabled")
    }
  },

  methods: {
    select() {
      this.$router.push({ name: "actions", params: { tx_id: this.productData.tx, pool_info: this.poolInfo, pool_idx: this.productIndex } });
    },

    goToBuy($link) {
      window.open($link);
    },

    async loopGetInfo() {
      if(this.querying) {
        return
      }

      this.querying =  true
      this.poolInfo = await opt.poolInfo(this.productIndex)
      window.allPoolInfo[this.productIndex] = this.poolInfo
      this.querying = false
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.product-div {
  border-width: 1px;
  border-style: solid;
  border-color: $divBorderColor;
  border-image: initial;
  border-radius: 15px;
  padding: 25px 15px 15px ;
  margin-top: 15px !important;
  background-color: $divBg;

  .product-logo {
    width: 80px;
    height: 80px;
  }
  .product-logo-div {
    background-color: $bg2;
    font-size: 36px;
    height: 120px;
    width: 120px;
    border-radius: 60px;
    -webkit-box-align: center;
    align-items: center;
    display: flex;
    -webkit-box-pack: center;
    justify-content: center;
    box-shadow: $btnBoxShadow-sushi 4px 4px 8px inset, $btnBoxShadow-sushi-2 -6px -6px 12px inset;
    margin: 0 auto 16px;
  }

  .pdt-name {
    font-family: $fontTit;
    font-size: 24px;
    font-weight: 700;
  }

  .pdt-txt {
    font-family: $fontTxt;
    font-size: 16px;
    font-weight: 400;
    color: $txtColor;
  }

  .col-btn-l {
    padding-left: 0 !important;
    padding-right: 6px !important;
  }
  .col-btn-r {
    padding-left: 6px !important;
    padding-right: 0 !important;
  }
  .btn-select {
    -webkit-box-align: center;
    align-items:center;
    background-color: $btnBg2;
    border: 0;
    border-radius: 28px;
    box-shadow: $btnBoxShadow-sushi 2px 2px 4px, $btnBoxShadow-sushi-2 -2px -2px 4px -1px;
    color: $btnFontColor;
    cursor: pointer;
    display: flex;
    font-size: 16px;
    font-weight: 700;
    height: 56px;
    -webkit-box-pack: center;
    justify-content: center;
    outline: none;
    padding-left: 24px;
    padding-right: 24px;
    width: 100%;
    margin: 20px 0 !important;
    outline: $walletBtnOutline;
  }
  .btn-select:hover,
  .btn-select:focus,
  .btn-select:active {
    border-width: 1px;
    border-style: solid;
    border-color: $walletBtnColor;
    border-image: initial;
    border-radius: 28px;
    outline: $walletBtnOutline;
    color: $walletBtnColor;
    background-color: $walletBtnBg;
  }
  .btn-select:disabled:hover {
    border: 0;
    color: $walletBtnBg;
    background-color: $walletBtnColor;
  }

  .pdt-apy {
    background-color: $btnFontColor;
    color: $txtColor;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 5px;
    font-size: 12px;
    padding: 5px;
  }
}
</style>

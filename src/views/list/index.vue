<template>
  <div class="list-container">
    <div class="row">
      <div class="col-xl-4 col-lg-4 col-md-4 col-sm-12 col-xs-12 text-center"
           v-for="(p, index) in products" :key="index">
        <div class="lists-div">
          <div class="row">
            <div class="col">
              <div class="product-logo-div">
                <img class="product-logo" :src="p.img" alt=""/>
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <p class="pdt-name">{{ p.name }}</p>
            </div>
          </div>
          <div class="row">
            <div class="col text-left">
              <p class="pdt-tit">{{ $t("list.info") }}</p>
              <p class="pdt-txt">{{ $t("token.name") }} {{ $t("list.total") }}{{ p.totalSupply }}</p>
            </div>
          </div>
          <div class="row">
            <div class="col text-left">
              <p class="pdt-tit">{{ $t("list.deposit") }}</p>
              <p class="pdt-txt">{{ $t("list.total") }}{{ p.chainInfo.poolStaked }}</p>
              <p class="pdt-txt">{{ $t("list.your") }}{{ p.chainInfo.userStaked }}</p>
            </div>
          </div>
          <div class="row">
            <div class="col text-left">
              <p class="pdt-tit">{{ $t("list.earned") }}</p>
              <p class="pdt-txt">{{ $t("list.available") }}{{ p.chainInfo.userReward }}</p>
              <p class="pdt-txt">{{ $t("list.hReward") }}{{ p.chainInfo.rewardPerHour }}</p>
              <p class="pdt-txt">{{ $t("list.dReward") }}{{ p.chainInfo.rewardPerDay }}</p>
              <p class="pdt-txt">{{ $t("list.wReward") }}{{ p.chainInfo.rewardPerWeek }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col d-flex justify-content-around">
        <button type="submit" class="d-wallet-btn" @click="seeTheMenu">
          <img class="d-wallet-btn-icon text-right" :src="'/static/logo/logo.png'" alt=""/>
          <h2 class="d-wallet-btn-name text-left">{{ $t("wallet.seeTheMenu") }}</h2>
        </button>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import chainOpt from "@/utils/web3/chainOperation";
import {rewardPerBlock, LPTokenToPoolID} from "@/utils/web3/constant";
import Decimal from "decimal.js";

let opt = chainOpt.opt

let defaultChainInfo = {
  supply: 0,

  userStaked: 0,
  poolStaked: 0,

  rewardPerHour: 0,
  rewardPerDay: 0,
  rewardPerWeek: 0,
}

export default {
  name: "List",
  components: { Loading },

  data() {
    return {
      queryingFlag: [],

      allBalance: null,
      poolInfo: [],
      loading: false,

      allTotal: 0,
      depositTotal: 0,
      yourTotal: 0,
      rewards: 0,
      hourRewards: 0,
      dayRewards: 0,
      weekRewards: 0,
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
          dateTime: "2020-09-23 20:00:00",
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

  mounted() {
    this.queryInfo()
  },

  methods: {
    updatePoolInfo(pid, pool, orgPid) {
      if(!pool) {
        return
      }

      let perBlockReward = new Decimal(rewardPerBlock).mul(pool.poolWeightPercent)

      console.log([pid, pool, orgPid, pool.poolWeightPercent])
      let chainInfo = {
        supply: new Decimal(16000).mul(pool.poolWeightPercent).toDP(6, Decimal.ROUND_FLOOR).toString(),
        userStaked: pool.user.stakeIn.toString(),
        userReward: pool.userToCollect,
        poolStaked: pool.pool.staked.toString(),

        rewardPerHour: perBlockReward.mul(28745 / 24).toDP(0, Decimal.ROUND_HALF_CEIL).toString(),
        rewardPerDay: perBlockReward.mul(28745).toDP(0, Decimal.ROUND_HALF_CEIL).toString(),
        rewardPerWeek: perBlockReward.mul(7 * 28745).toDP(0, Decimal.ROUND_HALF_CEIL).toString(),
      }

      let org = this.products[orgPid]
      org.chainInfo = chainInfo
      this.$set(this.products, orgPid, org);
    },

    seeTheMenu() {
      this.$router.push({name: "Dashboard"})
    },

    async loopQueryGlobalPoolInfo(pid) {
      await this.queryGlobalPoolInfo(pid)
      setTimeout(_ => {
        this.loopQueryGlobalPoolInfo(pid)
      }, 3000)
    },

    async queryGlobalPoolInfo(pid) {
      if (this.queryingFlag[pid]) {
        return
      }
      this.queryingFlag[pid] = true

      let product = this.products[pid]
      if(!product.stat) {
        return
      }

      let orgPid = pid
      pid = LPTokenToPoolID(this.products[pid].LPToken)

      if (!window.allPoolInfo[pid]) {
        let pool = await opt.poolInfo(pid)
        this.queryingFlag[pid] = false
        this.updatePoolInfo(pid, pool, orgPid)
        return
      }

      let pool = window.allPoolInfo[pid]
      this.updatePoolInfo(pid, pool, orgPid)
      this.queryingFlag[pid] = false
    },

    queryInfo() {
      this.products.forEach((p, i)=>{
        if(p.stat) {
          this.loopQueryGlobalPoolInfo(i)
        }
      })
    }
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.list-container {
  .lists-div {
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 15px;
    padding: 25px 15px 15px;
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

    .pdt-tit {
      font-family: $fontTit;
      font-size: 16px;
      font-weight: 700;
      color: $divBorderColor;
    }

    .pdt-txt {
      font-family: $fontTxt;
      font-size: 14px;
      font-weight: 400;
      color: $txtColor;
    }
  }

  .d-wallet-btn {
    height: 80px;
    width: 290px;
    display: flex;
    justify-content: center;
    -webkit-box-align: center;
    align-items: center;
    opacity: 1;
    cursor: pointer;
    border-radius: 40px;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    color: $walletBtnColor;
    background-color: $btnBg;
    box-shadow: $btnBoxShadow 0 2px 4px 2px;
    transition: all 0.1s ease 0s;
    outline: $walletBtnOutline;
    margin: 45px 0 !important;
  }
  .d-wallet-btn:hover,
  .d-wallet-btn:focus,
  .d-wallet-btn:active {
    border-width: 1px;
    border-style: solid;
    border-color: $walletBtnColor;
    border-image: initial;
    border-radius: 40px;
    outline: $walletBtnOutline;
    color: $walletBtnBg;
    background-color: $walletBtnColor;
  }
  .d-wallet-btn-name {
    margin: 0 0 0 16px;
    letter-spacing: 2px;
    font-family: $fontTit;
    text-transform: capitalize;
    font-size: 18px;
  }
  .d-wallet-btn-icon {
    height: 40px;
    width: 40px;
  }
}

</style>

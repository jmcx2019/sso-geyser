<template>
  <div class="wallet-container">
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
      <div class="col text-center welcome-txt">{{ $t("security.txt") }}</div>
    </div>

    <div class="row index-countdown">
      <div class="col text-center">
        <countdown :big-text="true" :date-time="'2020-09-23 20:00:00'" :tit-prefix="'开启'"/>
      </div>
    </div>

    <div class="row">
      <div class="col-xl-6 col-lg-6 col-md-6 col-sm-12 col-xs-12">
        <div class="balance-col-div">
          <div class="row">
            <div class="col-3 d-flex justify-content-around">
              <img class="geyser-logo" :src="'/static/logo/logo.png'" alt=""/>
            </div>
            <div class="col-9">
              <p class="preview-tit">{{ $t("home.balance") }}</p>
              <p v-if="userBalance === false" class="preview-txt">{{ $t("home.status") }}</p>
              <p v-if="userBalance !== false" class="preview-txt">{{ this.$HelperTools.toFinancialVal(userBalance) }}</p>
            </div>
          </div>
          <div class="row stats-div">
            <div class="col stats-txt">{{ $t("home.pending") }}</div>
            <div class="col text-right stats-txt">{{ this.$HelperTools.toFinancialVal(tokenBalance) }} {{ $t("token.name") }}</div>
          </div>
        </div>
      </div>
      <div class="col-xl-6 col-lg-6 col-md-6 col-sm-12 col-xs-12">
        <div class="supply-col-div">
          <div class="row">
            <div class="col">
              <p class="preview-tit">{{ $t("home.total") }}</p>
              <p v-if="!totalReward" class="preview-txt">{{ $t("home.status") }}</p>
              <p v-if="totalReward" class="preview-txt">{{ this.$HelperTools.toFinancialVal(totalReward) }}</p>
            </div>
          </div>
          <div class="row stats-div">
            <div class="col-7 stats-txt">{{ $t("home.rewards") }}</div>
            <div class="col-5 text-right stats-txt">{{ rewardsBalance }} {{ $t("token.name") }}</div>
          </div>
        </div>
      </div>
      <div class="col-xl-6 col-lg-6 col-md-6 col-sm-12 col-xs-12">
        <div class="supply-col-div">
          <div class="row">
            <div class="col">
              <p class="preview-tit">{{ $t("home.tlv") }}</p>
              <p v-if="!totalReward" class="preview-txt">{{ $t("home.status") }}</p>
              <p v-if="totalReward" class="preview-txt">{{ this.$HelperTools.toFinancialVal(UserTLV) }}</p>
            </div>
          </div>
          <div class="row stats-div">
            <div class="col-7 stats-txt">{{ $t("home.twl") }}</div>
            <div class="col-5 text-right stats-txt">{{ this.$HelperTools.toFinancialVal(TLV) }}</div>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <div class="wallet-connect-div">
          <div class="row">
            <div v-show="walletConnectView" class="col text-center wallet-connect-tit">
              {{ $t("wallet.connect") }}
            </div>
          </div>

          <div class="row">
            <div class="col d-flex justify-content-around">
              <button v-show="walletConnectView" type="submit" class="wallet-btn" @click="openMetamask">
                <img class="wallet-btn-icon text-right" :src="'/static/logo/logo.png'" :alt="$t('wallet.metamask')"/>
                <h2 class="wallet-btn-name text-left">{{ $t("wallet.name") }}</h2>
              </button>
              <button v-show="!walletConnectView" type="submit" class="wallet-btn" @click="seeTheMenu">
                <img class="wallet-btn-icon text-right" :src="'/static/logo/logo.png'" alt=""/>
                <h2 class="wallet-btn-name text-left">{{ $t("wallet.seeTheMenu") }}</h2>
              </button>
            </div>
          </div>

          <div v-show="!walletConnectView" class="row">
            <div class="col d-flex justify-content-around">
              <button type="submit" class="wallet-btn" @click="seeTheList">
                <img class="wallet-btn-icon text-right" :src="'/static/logo/logo.png'" alt=""/>
                <h2 class="wallet-btn-name text-left">{{ $t("list.seeTheList") }}</h2>
              </button>
            </div>
          </div>

          <div class="row" v-show="walletConnectError">
            <div class="col text-center wallet-error-txt">
              {{ $t("wallet.error") }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import chainOpt from "../../utils/web3/chainOperation";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import Decimals from "decimal.js"
import {startBlock, rewardPerBlock, halfStartBlock, halfRewardPerBlock, maxReward} from "@/utils/web3/constant";
import {uniAddrs} from "@/utils/web3/constant";
import Countdown from "@/components/Countdown";
import {getTLV, getUserTLV} from "@/utils/web3/totalLockedValue";

window.mingingPoolInfo = {}
let opt = chainOpt.opt
let queryingTLV = false

export default {
  name: "Wallet",
  components: {Countdown, Loading },

  data() {
    return {
      ConnectWalletModalText: 'Waiting for connection',
      loading: false,
      walletConnectView: true,
      walletConnectError: false,

      tokenBalance: "0",
      rewardsBalance: "0.0795",

      totalReward: false,
      userBalance: false,

      TLV: "0",
      UserTLV: "0"
    };
  },

  mounted() {
    setTimeout(_=>{
      if(chainOpt.wallet.isConnected()) {
        this.setWalletConnected()
      }
    }, 2000)

    if(!queryingTLV) {
      queryingTLV = true
      setInterval(_=>{
        this.TLV = getTLV()
        this.UserTLV = getUserTLV()
      }, 1000)
    }

  },

  methods: {
    async updatePoolInfo() {
      let totalCollected = new Decimals(0);
      for(let i=0; i<uniAddrs.length; i++) {
        try {
          let pool = await opt.poolInfo(i)
          totalCollected = totalCollected.add(pool.userToCollect)
          this.$nextTick(_=>{
            this.tokenBalance = totalCollected.toDP(6, Decimals.ROUND_FLOOR).toString()
          })
        } catch (e) {}
      }
    },

    setWalletConnected() {
      this.updatePoolInfo()
      opt.getBlockNum()
          .then(r=>{
            if(r > 0 && r > startBlock) {
              let totalBlocks = new Decimals(r).sub(startBlock)
              let halfBlocks = 0
              if(r > halfStartBlock) {
                totalBlocks = new Decimals(halfStartBlock).sub(startBlock)
                halfBlocks = new Decimals(r).sub(halfStartBlock)
                this.rewardsBalance = new Decimals(halfRewardPerBlock).toDP(4, Decimals.ROUND_FLOOR).toString()
              }
              let totalReward = new Decimals(rewardPerBlock).mul(totalBlocks)
              let halfReward = new Decimals(halfRewardPerBlock).mul(halfBlocks)

              let total = totalReward.add(halfReward)
              if(total.gt(maxReward)) {
                total = new Decimals(maxReward)
              }

              this.totalReward = total.toDP(4, Decimals.ROUND_FLOOR).toString()
            } else {
              this.totalReward = "0"
            }
          })

      opt.userTokenBalance()
          .then(r=>{
            this.userBalance = r
          })
      this.loading = false
      this.walletConnectView = false
    },

    async openMetamask() {
      //wallet connect
      this.loading = true

      if(!chainOpt.wallet.isConnected()) {
        chainOpt.wallet.connect()
          .then(r=>{
            if(r === null) {
              //not connect, do nothing
              this.loading = false
              return
            }

            this.setWalletConnected()
          })
          .catch(()=>{
            //unknown error, do nothing
            this.loading = false
            this.walletConnectError = true
          })
      } else {
        //already connected
        this.setWalletConnected()
      }
    },
    seeTheMenu() {
      this.$router.push({name: "Dashboard"})
    },
    seeTheList() {
      this.$router.push({name: "List"})
    },
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";
.wallet-container {
  max-width: 800px;
  margin: auto;
  height: 100%;
  padding-top: 15px;
  //padding-bottom: 30px;

  .home-logo{
    width: 120px;
    height: 120px;
    margin-bottom: 15px !important;
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

  .balance-col-div,
  .supply-col-div {
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 15px;
    margin-bottom: 15px !important;
    padding: 15px 0;
    background-color: $divBg;
  }

  .geyser-logo {
    width: 60px;
    height: 60px;
    margin: auto;
  }

  .preview-tit {
    font-size: 14px;
    font-family: $fontTxt;
    color: $txtColor;
  }
  .preview-txt {
    font-size: 27px;
    font-family: $fontTit;
    color: $titColor;
  }

  .stats-div {
    border-top-width: 1px;
    border-style: solid none none none;
    border-color: $divBorderColor;
    padding-top: 15px;
  }
  .stats-txt {
    font-size: 14px;
    font-family: $fontTxt;
    color: $txtColor;
  }

  .wallet-connect-div {
    //border-width: 1px;
    //border-style: solid;
    //border-color: $divBorderColor;
    //border-image: initial;
    //border-radius: 0;
    padding: 30px 15px;
    //margin-bottom: 24px !important;
  }

  .wallet-connect-tit {
    font-size: 16px;
    color: $txtColor;
    line-height: 28px;
    font-family: $fontTxt;
    margin-bottom: 20px;
  }
  .wallet-error-txt {
    font-size: 16px;
    color: $errColor;
    line-height: 28px;
    font-family: $fontTxt;
    margin-top: 15px;
  }

  .wallet-btn {
    height: 80px;
    width: 290px;
    display: flex;
    justify-content: center;
    -webkit-box-align: center;
    align-items: center;
    opacity: 1;
    cursor: pointer;
    border-radius: 40px;
    border-width: 2px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    color: $walletBtnColor;
    background-color: $btnBg;
    box-shadow: $btnBoxShadow-sushi 2px 2px 4px, $btnBoxShadow-sushi-2 -2px -2px 4px -1px;
    transition: all 0.1s ease 0s;
    outline: $walletBtnOutline;
    margin-bottom: 15px;
  }
  .wallet-btn:hover,
  .wallet-btn:focus,
  .wallet-btn:active {
    border-width: 1px;
    border-style: solid;
    border-color: $walletBtnColor;
    border-image: initial;
    border-radius: 40px;
    outline: $walletBtnOutline;
    color: $walletBtnBg;
    background-color: $walletBtnColor;
  }
  .wallet-btn-name {
    margin: 0 0 0 16px;
    letter-spacing: 2px;
    font-family: $fontTit;
    text-transform: capitalize;
    font-size: 18px;
  }
  .wallet-btn-icon {
    height: 40px;
    width: 40px;
  }

  .index-countdown {
    margin-bottom: 30px;
  }
}
</style>

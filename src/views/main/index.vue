<template>
  <div class="container-fluid main-container">
    <wallet></wallet>

    <div class="row">
      <div class="col text-center">
        <div class="security-tit">{{ $t("security.audit") }}</div>
        <img class="security-logo" :src="'/static/security/knownsec-b.png'" alt=""/>
      </div>
    </div>
  </div>
</template>

<script>
import Wallet from "./Wallet";

export default {
  components: { Wallet },
};
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.main-container {
  height: 100%;

  .security-tit {
    margin-top: 45px;
    color: $txtColor;
    font-family: $fontTxt;
  }
  .security-logo {
    margin-top: 10px;
    margin-bottom: 30px;
    height: 35px;
  }
}
</style>

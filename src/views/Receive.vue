<template>
  <div>
    <nut-navbar @on-click-back="back" :leftShow="true" :rightShow="false">收款</nut-navbar>
    <WalletInfo/>
  </div>
</template>

<script>
// @ is an alias to /src
import WalletInfo from "@/components/WalletInfo";
import Footer from "@/components/Footer";

export default {
  name: "home",
  components: {
    WalletInfo,
    Footer
  },
  methods: {
    back() {
      this.$router.push({
        path: "/",
        name: "Wallet"
      });
    }
  }
};
</script>

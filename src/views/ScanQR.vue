<template>
  <div class="home">
    <nut-navbar @on-click-back="back" @on-click-more="scan" :leftShow="true" :rightShow="false">扫码</nut-navbar>
    <QRScanner/>
  </div>
</template>

<script>
// @ is an alias to /src
import QRScanner from "@/components/QRScanner";

export default {
  name: "home",
  components: {
    QRScanner
  },
  methods: {
    back() {
      window.location.href="http://www.libra-wallet.xyz/#/send"
      // this.$router.push({ name: "Send" });
    }
  }
};
</script>

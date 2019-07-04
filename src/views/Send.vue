<template>
  <div class="send">
    <nut-navbar @on-click-back="back" @on-click-more="scan" :leftShow="true" :rightShow="true">
      付款
      <a slot="more-icon">
        <img src="../assets/img/scan.png" width="30" style="margin-top:5px" alt />
      </a>
    </nut-navbar>
    <Transfer />
  </div>
</template>

<script>
// @ is an alias to /src
import Transfer from "@/components/Transfer";

export default {
  name: "send",
  components: {
    Transfer
  },
  methods: {
    back() {
      this.$router.push({ name: "Wallet" })
    },
    scan() {
      window.location.href="https://www.libra-wallet.xyz/#/scanQR"
      // this.$router.push({name: "ScanQR"})
    }
  }
}
</script>

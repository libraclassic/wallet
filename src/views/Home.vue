<template>
  <div>
    <Wallet />
  </div>
</template>

<script>
import Wallet from "@/components/Wallet";

export default {
  name: "home",
  components: {
    Wallet
  },
  created() {
    if (this.$route.query && this.$route.query.address) {
      this.$router.push({
        name: "Send",
        query: {
          address: this.$route.query.address
        }
      });
    }
  }
};
</script>

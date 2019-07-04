<template>
  <div class="Wallet">
    <VueElementLoading
      :active="isTransfering"
      spinner="bar-fade-scale"
      color="#9b67df"
      text="支付中，请稍后 ..."
      is-full-screen
    />
    <section class="wallet-container">
      <div class="wallet-body">
        <div class="input-box">
          <b-field label="对方账户">
            <b-input v-model="address" placeholder="请输入对方账户"/>
          </b-field>
        </div>
        <div class="input-box">
          <b-field label="金额">
            <b-input v-model="amount" placeholder="请输入金额" type="number"/>
          </b-field>
        </div>
        <div class="button-box">
          <b-button type="is-success" @click="transfer">确认支付</b-button>
          <b-button @click="back">取消支付</b-button>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import config from "../config";
import axios from "axios";
import { mapActions, mapState } from "vuex";
import VueElementLoading from "vue-element-loading";

export default {
  name: "Wallet",
  data() {
    return {
      address: "",
      amount: "",
      isTransfering: false
    };
  },
  components: {
    VueElementLoading
  },
  created() {
    if (!this.mnemonic) {
      this.$router.push({ name: "Wallet" });
    }
    if (this.$route.query && this.$route.query.address) {
      // this.address = this.$route.query.to;
      // this.address = this.$route.params.address;
      this.address=this.$route.query.address
      this.updateUserAddress(this.address);
    }
  },
  computed: {
    ...mapState({
      userAddress: state => state.userAddress,
      mnemonic: state => state.mnemonic
    })
  },
  methods: {
    ...mapActions({
      updateUserAddress: "updateUserAddress"
    }),
    back() {
      this.$router.push({ name: "Wallet" });
    },
    async transfer() {
      if (this.address.length == 64 && this.amount > 0) {
        this.isTransfering = true;
        await axios.post(config.api + "/transfer", {
          fromAddress: this.userAddress,
          mnemonic: this.mnemonic,
          toAddress: this.address,
          amount: this.amount
        });
        this.isTransfering = false;
        this.$router.push({ name: "Wallet" });
      } else {
        this.$dialog({
          title: "提示",
          content: "请输入地址 / 金额",
          noCancelBtn: true,
          onOkBtn(event) {
            //确定按钮点击事件
            this.close(); //关闭对话框
          }
        });
      }
    }
  }
};
</script>

<style lang="scss">
.input-box {
  text-align: left;
  margin-bottom: 20px;
  .field {
    label {
      color: #fff;
    }
  }
}
</style>

<style lang="scss" scoped>
.wallet-container {
  min-height: calc(100vh - 170px);
  width: 100%;
  margin: 0 auto;
}
.wallet-body {
  padding: 20px;
}
.button-box {
  margin-top: 55px;
  button {
    width: 100%;
    height: 60px;
    margin-bottom: 20px;
  }
}
/* on desktop */
@media only screen and (min-width: 1025px) {
  .wallet-container {
    margin: 10px auto;
    width: 70%;
    border-radius: 3px;
  }
  .wallet-body {
    padding: 50px;
  }
  .button-box {
    button {
      width: 40%;
      height: 60px;
      max-width: 300px;
    }
    button:first-child {
      margin-right: 15px;
    }
    button:last-child {
      margin-left: 15px;
    }
  }
}
</style>

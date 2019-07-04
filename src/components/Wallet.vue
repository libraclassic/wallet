<template>
  <section class="wallet-container">
    <div class="wallet-body">
      <div v-if="!this.userAddress" class="creating-wallet">
        <span>正在创建钱包 ...</span>
        <vue-loading type="bars" color="#7957d5" :size="{ width: '50px', height: '50px' }" />
      </div>
      <div v-else>
        <div v-if="balance" class="balance">
          <nut-price class="price" :price="balance" :needSymbol="false" :thousands="true" />
          <span style="color:#fff">Libra</span>
          <!-- <span>{{ balance | numberWithComma }}</span> -->
        </div>
        <div v-else class="balance">
          <span>加载中 ...</span>
        </div>
        <div class="refresh">
          <a @click="refreshBalance">
            <b-icon icon="refresh" size="is-small" />
            {{ isQueryBalance ? '获取中。。。' : '刷新余额' }}
          </a>
        </div>
        <div class="button-box">
          <b-button icon-left="send" @click="openSend">付款</b-button>
          <b-button icon-left="qrcode" @click="openReceive">收款</b-button>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import config from "@/config.json";
import axios from "axios";
import UserDataPersistance from "@/userData/persistance";
import Avatar from "@/components/Avatar.vue";
import { mapActions, mapState } from "vuex";

export default {
  name: "Wallet",
  components: {
    Avatar
  },
  filters: {
    numberWithComma(x) {
      x = x.toString();
      const pattern = /(-?\d+)(\d{3})/;
      while (pattern.test(x)) {
        x = x.replace(pattern, "$1,$2");
      }
      return x;
    }
  },
  data() {
    return {
      network: null,
      message: "",
      byteLength: "",
      transactionHash: "",
      userData: null,
      updateingBalance: false,
      isQueryBalance: false
    };
  },
  async created() {
    // Load from local storage
    this.userData = new UserDataPersistance();
    // Already exist
    if (this.userData.userAddress) {
      this.updateUserData({
        userAddress: this.userData.userAddress,
        userAddressShort: this.userData.userAddress.substring(0, 5),
        balance: this.userData.balance,
        mnemonic: this.userData.mnemonic
      });
      await this.queryBalance();
    } else {
      // Create a new wallet
      const data = await this.createNewWallet();
      await this.updatePersistance(data.address, data.balance, data.mnemonic);
    }
    let _self = this;
    // setInterval(() => {
    //   _self.refreshBalance();
    // }, 2000);
  },
  computed: {
    ...mapState({
      userAddress: state => state.userAddress,
      balance: state => state.balance,
      mnemonic: state => state.mnemonic
    }),
    shortUserAddr() {
      if (!this.userAddress) return "Loading...";
      const first = this.userAddress.substr(0, 7);
      const last = this.userAddress.substr(
        this.userAddress.length - 7,
        this.userAddress.length
      );
      return `${first}...${last}`;
    }
  },
  methods: {
    ...mapActions({
      updateUserData: "updateUserData",
      updateBalance: "updateBalance"
    }),
    async createNewWallet() {
      try {
        const { data } = await axios.post(config.api + "/createWallet");
        this.updateUserData({
          userAddress: data.address,
          userAddressShort: data.address.substring(0, 5),
          balance: data.balance,
          mnemonic: data.mnemonic
        });
        return data;
      } catch (error) {
        console.log(error);
      }
    },
    async refreshBalance() {
      this.isQueryBalance = true;
      await this.queryBalance();
      this.isQueryBalance = false;
    },
    async queryBalance() {
      let res = await axios.get(
        config.api + `/api/balance/${this.userAddress}`
      );
      let balance = res.data;
      console.log(balance);
      this.updateBalance(balance);
      this.userData.updateUserBalance(balance);
      return balance;
    },
    async updatePersistance(userAddress, balance, mnemonic) {
      this.userData.update(userAddress, balance, mnemonic);
      this.userData.save();
    },
    openSend() {
      // this.$router.push({ name: 'ScanQR' })
      this.$router.push({ name: "Send" });
    },
    openReceive() {
      this.$router.push({
        name: "Receive",
        query: { address: this.userAddress }
      });
    },
    async doUpdateBalance() {
      this.updateingBalance = true;
      // this.$toast.open("Updating balance....");
      let response = await this.queryBalance();
      if (response) {
        this.updateingBalance = false;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.wallet-container {
  // box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.5);
  min-height: calc(100vh - 170px);
  width: 100%;
  margin: 0 auto;
}
.price {
  zoom: 2;
}
.nut-price {
  color: #fff;
}
.price-big {
  font-size: 36px;
}
.wallet-body {
  padding-bottom: 2rem;
  padding-top: 2rem;
}
.creating-wallet {
  padding-top: 50px;

  span {
    font-weight: bold;
    font-size: 21px;
    margin-bottom: 20px;
    display: block;
  }
}
.wallet-address {
  width: 100%;
  margin-top: 10px;
  text-align: center;
  color: #f7f7f76b;
}
.nut-price .price-big {
  font-size: 40px;
}
.nut-price .price-small {
  font-size: 20px;
}
// .balance {
//   margin-top: 25px;
//   img {
//     width: 35px;
//     height: 35px;
//     margin-right: 10px;
//     vertical-align: middle;
//   }
//   span {
//     font-size: 40px;
//     font-weight: bold;
//     vertical-align: middle;
//   }
// }
.refresh {
  a {
    color: #f7f7f76b;
    font-size: 13px;
  }
}
.button-box {
  margin-top: 25px;
  padding: 0 30px;
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
    padding-bottom: 6rem;
    padding-top: 6rem;
  }
  .button-box {
    margin-top: 60px;
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

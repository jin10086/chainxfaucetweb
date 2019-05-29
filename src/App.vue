<template>
  <div id="app">
    <nav class="navbar navbar-default">
      <h1 class="container-fluid">Chainx PCX Faucet</h1>
    </nav>
    <section class="container">
      <div class="panel panel-default">
        <!-- <div class="panel-heading">
          <h3>水龙头</h3>
        </div>-->
        <div class="panel-body">
          <div class="input-group1">
            <span class="input-group-addon">address:</span>
            <input
              v-model="userPCXAddress"
              type="text"
              class="form-control"
              placeholder="请输入你的PCX地址"
            >
          </div>
          <button class="btn btn-success" style="margin: 4px;" v-on:click="getfree">获取手续费</button>
        </div>
      </div>
      <div class="panel panel-default">
        <div class="panel-heading">
          <h3>水龙头账号</h3>
        </div>
        <div class="panel-body">
          <div>地址: 5T1DD7nEXvBmVXYCQFaZ4vEhVw1kDSJivZty7t7pdf2wQyF8</div>
          <div>余额: {{ pcxBalance/1e8.toFixed(4) }} PCX</div>
        </div>
      </div>
      <div class="panel panel-default">
        <div class="panel-heading">
          <h3>transactions</h3>
        </div>
        <div class="panel-body" style="flex-direction: column; display: flex;">
          <a :href="txhashUrl">{{txhash}}</a>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import { all } from "q";
const Chainx = require("chainx.js").default;
const axios = require("axios");
const chainx = new Chainx("wss://w1.chainx.org/ws");

export default {
  name: "app",
  components: {},
  data() {
    return {
      pcxBalance: null,
      userPCXAddress: null,
      txhash: null
    };
  },
  mounted() {
    axios
      .get(
        "https://api.chainx.org/account/0x94dd54692fa4fa3909603c9c9332663d5fcb805c7848f760359461dd21283ca5/balance?native=true"
      )
      .then(response => (this.pcxBalance = response.data[0].Free))
      .catch(error => console.log(error));
  },
  methods: {
    getfree: function() {
      if (this.userPCXAddress) {
        let hexAddress = chainx.account.decodeAddress(this.userPCXAddress);

        //{"status":1,"msg":"转账成功","txHash":"0x1a0cd452312cb09533fed346f38fbfdb09b5e8154561b20721f5a60be347ced0"}#
        //{msg: "PCX余额不为空",status: 0}
        //成功则 status为1,则显示txHash，并 生成这样的网址https://scan.chainx.org/txs/0x1a0cd452312cb09533fed346f38fbfdb09b5e8154561b20721f5a60be347ced0
        //失败的话 则 显示masg里面的内容
        axios
          .get("http://101.37.151.113/" + hexAddress)
          .then(function(response) {
            if (response.status === 0) {
              alert(response.msg);
            } else {
              this.txhash = response.txHash;
            }
          })
          .catch(error => console.log(error));
      }
    }
  },
  computed: {
    txhashUrl: function() {
      return "https://scan.chainx.org/txs/" + this.txhash;
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  width: 800px;
  margin: 60px auto;
  border: 1px solid rgba(0, 0, 0, 0.1);
}
.panel {
  margin-bottom: 40px;
}
.input-group1 {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.input-group-addon {
  margin-right: 10px;
}
</style>

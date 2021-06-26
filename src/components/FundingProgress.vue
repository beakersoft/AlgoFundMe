<template>
  <div>
    Our total amount so far is {{ amountInAlgos }} algos<br />

    <div class="progress-box">
      <p class="progress-percent">
        You are <strong class="is-size-4">{{ goalPercentage }}%</strong> complete towards your goal of {{goalAmount}} algos!     
      </p>
      <progress class="progress is-success" :value="goalPercentage" max="100">
        {{ goalPercentage }}%
      </progress>
    </div>
  </div>
</template>

<script>
const passPhrase = '[YourDonationWalletPassPhraseHere]'
const apiKey = "[YourAPIKeyHere]"
const algosdk = require("algosdk")
const baseServer = "https://testnet-algorand.api.purestake.io/ps2"
const port = ""
const token = {
  "X-API-Key": apiKey,
}
const algodClient = new algosdk.Algodv2(token, baseServer, port)

export default {
  computed: {
    amountInAlgos() {
      //1 Algo equals 1000000 microAlgos
      if(this.accountAmount === null || this.accountAmount === 0)
        return 0

      return this.accountAmount / 1000000
    },
    goalPercentage() {
      return ((this.amountInAlgos/this.goalAmount) * 100).toFixed(1)
    },
  },
  data: function () {
    return {
      accountAmount: null,
      goalAmount: 10,
    };
  },
  created() {
    this.getCurrentBalance();
  },
  methods: {
    async getCurrentBalance() {
      let account = algosdk.mnemonicToSecretKey(passPhrase);
      let accountInfo = await algodClient.accountInformation(account.addr).do();
      this.accountAmount = accountInfo.amount;
    },
    createAccount() {
      var account = algosdk.generateAccount();
      var passphrase = algosdk.secretKeyToMnemonic(account.sk);
      console.log("My address: " + account.addr);
      console.log("My passphrase: " + passphrase);
    },
    async getStatus() {
      let status = await algodClient.status().do();
      console.log("Algorand network status: %o", status);
    },   
  },
};
</script>
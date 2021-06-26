<template>
  <div>
      Your account passphrase (in production you would just connect your wallet to the application)<br/>
      <input v-model="sendingWalletPassphrase" placeholder="passphrase"><br/>
      Amount of micoAlgos to donate<br/>
      <input v-model="donationAmount" placeholder="amount">
      <button @click="donate()">Donate</button>
      <p>
        {{confirmationStatus}}
      </p>
  </div>
</template>

<script>

const apiKey = "bzV4UzzZ5nbvKMqf6elm9IgtAW1HnCz5TIGD4L1j";
const algosdk = require("algosdk");
const baseServer = "https://testnet-algorand.api.purestake.io/ps2";
const port = "";
const token = {
  "X-API-Key": apiKey
};

const algodClient = new algosdk.Algodv2(token, baseServer, port);

export default {
data: function() {
      return {
        reciveAddress: 'FVSJBRUYRRJBSDBANTMG2QR6CIVI4Y4QVGLXKYPOTE5P5SRQK3KNG34AME',
        confirmationStatus: 'Waiting for donation...',
        sendingWalletPassphrase: 'always hamster shiver ahead wasp cable song village note require amateur celery electric notice rough sauce simple aisle kitchen elite income olympic goat abandon run',
        donationAmount: 1000000,
      }
  },
  methods: {
    async donate() {
      
      let account = algosdk.mnemonicToSecretKey(this.sendingWalletPassphrase)
      let params = await algodClient.getTransactionParams().do();
      let note = algosdk.encodeObj("My donation");

      let txn = algosdk.makePaymentTxnWithSuggestedParams(account.addr,this.reciveAddress, parseInt(this.donationAmount), undefined, note, params); 

      let signedTxn = txn.signTxn(account.sk);
      console.log(signedTxn)
      let txId = txn.txID().toString();
      this.confirmationStatus = 'Submiting donation please wait for confirmation...'

      await algodClient.sendRawTransaction(signedTxn).do();
      let confirmedTxn = await this.waitForConfirmation(algodClient, txId, 4);

      this.confirmationStatus = "Transaction " + txId + " confirmed in round " + confirmedTxn["confirmed-round"] + ", thank you!"
      
    },
    //transaction validation, lifted from https://developer.algorand.org/docs/build-apps/hello_world/
    async waitForConfirmation(algodClient, txId, timeout) {

        const status = (await algodClient.status().do());
        if (status === undefined) {
          this.confirmationStatus = "Confirmation failed: Unable to get node status"
        }

        const startround = status["last-round"] + 1
        let currentround = startround;

        while (currentround < (startround + timeout)) {
            const pendingInfo = await algodClient.pendingTransactionInformation(txId).do();
            if (pendingInfo !== undefined) {
                if (pendingInfo["confirmed-round"] !== null && pendingInfo["confirmed-round"] > 0) {
                    //Got the completed Transaction
                    return pendingInfo;
                } else {
                    if (pendingInfo["pool-error"] != null && pendingInfo["pool-error"].length > 0) {
                        // If there was a pool error, then the transaction has been rejected!
                        this.confirmationStatus = "Transaction " + txId + " rejected - pool error: " + pendingInfo["pool-error"];
                    }
                }
            }
            await algodClient.statusAfterBlock(currentround).do();
            currentround++;
        }

        this.confirmationStatus = "Transaction " + txId + " not confirmed after " + timeout + " rounds!"
    }
  },
}
</script>
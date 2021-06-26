# AlgoFundMe

Simple demo project to show how to create a simple go fund me donation style web application using the algorand blockchain.

The project is a Vue2 project that uses the algosdk sdk and the purestake api to talk to the algo test net and will require you to have an API key created on purestake. 

Head over to https://developer.purestake.io/ to create an account and get your key.

In order for the project to work you will then need to create 2 accounts on the algo test net, one that will be the donation account (receiver) and one that will be the donator account (giver)

Set these two accounts up, and make a note of the pass phrases and wallet addresses for them. Pick one account to be the donator account and head over to the https://bank.testnet.algorand.network/ page where you can top the account up with algos.

## Updating the source

Once you have these 2 accounts created and their details, along with funds in the donator account you need to update the components with these details so the demo will work

/components/Donate.Vue 
- [YourAPIKeyHere] needs to be the API key from purestake
- [YourReciveAddressHere] needs to be the wallet address where you want to send the donated funds to. It should be a long string of letters and numbers
- [YourSendingPassPhraseHere] needs to be the pass phrase of the account you are going to send funds from, the same one you added funds to on the test net. This will be a string of words

/components/FundingProgress.Vue
- [YourAPIKeyHere] needs to be the API key from purestake
- [YourDonationWalletPassPhraseHere] needs to be the pass phrase of the account you are donating to. This will be a string of words

Once these deatails are filled in you should be able to send using the donate page, and see the progress towards the goad on the progress page

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

Please not this is just a demo project, and has not been audited by any entity. Please dont use this code in production!
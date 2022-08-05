<template>
  <div class="card-component-class">
    <!-- connect-wallet button is visible if the wallet is not connected -->
    <div v-if="!connected">
      <button @click="onConnect()">Connect wallet</button>
      <h3>Please connect your wallet by clicking the button :D</h3>
    </div>

    <div v-if="connected">
      <!-- if connected call the contract for getting the text message -->
      <button @click="getMessage()">Get Message</button>

      <!-- if connected change the text value -->
      <button @click="changeMessage()">Change message</button>

      <input v-model="message" placeholder="new message" />

      <!-- display the account address -->
      <h3>Your account address : {{ account }}</h3>

      <!-- show the text message retreived from contract call -->
      <h3>Message : {{ contractResult }}</h3>
    </div>
  </div>
</template>

<script>
// import the web3 module and create a web3 instance
import Web3 from "web3/dist/web3.min.js";

// create a ethereum window using ethers.js
const { ethereum } = window;

const web3 = new Web3(ethereum);

// import the library to connect to the metamask
import detectEthereumProvider from "@metamask/detect-provider";

const abi = JSON.parse(
  `[{"inputs":[{"internalType":"string","name":"initialText","type":"string"}],"stateMutability":"nonpayable","type":"constructor"},{"inputs":[{"internalType":"string","name":"newText","type":"string"}],"name":"changeText","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"speak","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"}]`
);

const contractAddress = "0x6655995548512Ac7253b83AB7b28E33A9Ca230bD";

const contract = new web3.eth.Contract(abi, contractAddress);

export default {
  name: "App",

  // variables for storing the data
  data: () => {
    return {
      contractResult: "",
      network: "",
      account: "not connected",
      connected: false,
      message: "",
    };
  },

  // methods defined for caontracts
  methods: {
    // to connect to the metamask wallet
    onConnect() {
      const provider = detectEthereumProvider();
      if (provider) {
        web3.eth.getAccounts().then((accounts) => {
          this.account = accounts[0];
          console.log("The connected wallet is metamask");
        });
        this.connected = true;
      }
    },

    // method for calling the contract for retrieving the message
    getMessage() {
      contract.methods
        .speak()
        .call()
        .then((result) => (this.contractResult = result));
    },

    // method for updating the message
    changeMessage() {
      contract.methods.changeText(this.message).send({ from: this.account });
    },
  },
};
</script>

<style scoped>
.card-component-class {
  margin: auto;
  margin-top: 10%;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 30px;
  padding: 50px;
  height: 30vh;
  width: 30%;
}

button {
  margin-top: 10px;
  height: 40px;
  width: 100%;
  background-color: #8f8f8f;
  color: white;
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
}

input {
  margin-top: 10px;
  height: 40px;
  width: 98%;
  padding-left: 10px;
}

h3 {
  font-family: Arial, Helvetica, sans-serif;
  color: #6c6c6c;
}
</style>

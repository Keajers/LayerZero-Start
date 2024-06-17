# L0 - Beginning
https://layerzero.network/

Alright, let’s get you set up with LayerZero. First things first, you gotta make sure your rig is up to the task.

## System Requirements

Here’s the lowdown on what you need:

### Node.js 
https://nodejs.org/

You’ll need Node.js (v12 or higher). It’s the backbone for running scripts and managing packages.
    
### NPM or Yarn
Package managers like NPM or Yarn are a must for handling dependencies.

### Ethereum-compatible Wallet
MetaMask or any other wallet that supports Ethereum will do the trick. Have some testnet ETH for gas fees.
   
### Blockchain Node Access
Access to an Ethereum node (Infura, Alchemy, or a local Geth node). This is your gateway to the blockchain.

## Installation Process

Now, let's get down to the nitty-gritty. Installing LayerZero is pretty straightforward. Follow these steps, and you’ll be up and running in no time.

### Set Up Your Project

Start by creating a new directory for your project. Fire up your terminal and run:

```
mkdir layerzero-project
cd layerzero-project
```

### Initialize Your Project

If you haven’t already, initialize a new Node.js project:

```
npm init -y
```

### Install Dependencies

Now, let’s grab LayerZero and other necessary packages:

```
npm install @layerzerolabs/contracts @openzeppelin/contracts ethers hardhat
```

### Install Hardhat

If you don’t have Hardhat set up yet, install it:

```
npx hardhat
```

Choose “Create a basic sample project” and follow the prompts to set up your Hardhat environment.

## Initial Configuration

Alright, your project is set up, but we need to configure a few things to make sure everything runs smoothly.

### Hardhat Configuration

Open hardhat.config.js and configure your networks. Replace with your Infura or Alchemy keys:

```
require("@nomiclabs/hardhat-waffle");

module.exports = {
  solidity: "0.8.0",
  networks: {
    rinkeby: {
      url: `https://rinkeby.infura.io/v3/YOUR_INFURA_PROJECT_ID`,
      accounts: [`0x${YOUR_PRIVATE_KEY}`]
    }
  }
};
```

### Deploy Script

Create a scripts/deploy.js file to handle deployment:

```
async function main() {
  const [deployer] = await ethers.getSigners();
  console.log("Deploying contracts with the account:", deployer.address);

  const LayerZeroMessenger = await ethers.getContractFactory("LayerZeroMessenger");
  const endpoint = "YOUR_LAYERZERO_ENDPOINT_ADDRESS"; // Replace with the actual endpoint address for your testnet
  const messenger = await LayerZeroMessenger.deploy(endpoint);

  console.log("LayerZeroMessenger deployed to:", messenger.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### Smart Contract

Create a contracts/LayerZeroMessenger.sol file:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@layerzerolabs/contracts/lzApp/NonblockingLzApp.sol";

contract LayerZeroMessenger is NonblockingLzApp, Ownable {
    event MessageReceived(uint16 srcChainId, bytes srcAddress, uint64 nonce, bytes payload);

    constructor(address _endpoint) NonblockingLzApp(_endpoint) {}

    function sendMessage(uint16 _dstChainId, bytes calldata _dstAddress, bytes calldata _payload) external payable onlyOwner {
        _lzSend(_dstChainId, _dstAddress, _payload, payable(msg.sender), address(0x0), bytes(""));
    }

    function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64 _nonce, bytes memory _payload) internal override {
        emit MessageReceived(_srcChainId, _srcAddress, _nonce, _payload);
    }
}
```

### Deploy the Contract

Deploy your smart contract to the testnet:

```
npx hardhat run scripts/deploy.js --network rinkeby
```

And voila! You’ve got LayerZero up and running. Your contract is deployed, endpoints are configured, and you’re ready to start building some cross-chain magic. Happy coding!

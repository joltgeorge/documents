# Development Tutorial

## Introduction

Joltify provides a simple hardhat project for Solidity smart contract deployment and interaction on Joltify EVM co-chain.

{% embed url="https://github.com/carlthelegend/joltevm" %}
The simple hardhat project
{% endembed %}

## Step 1: Configuration setup

To access the Joltify local chain and Joltify Testnet chain, configure the corresponding networks in `joltevm/hardhat.config.js` as follows:

```javascript
networks: {
    testnet: {
      url: "testnet_rpc_url",
      accounts: ["account_private_key"]
    }
  }
```

Alternatively, you can also set up `accounts` with seed phrases as follows:

<pre class="language-javascript"><code class="lang-javascript"><strong>networks: {
</strong><strong>  testnet: {
</strong>      url: "testnet_rpc_url",
      accounts: {
        mnemonic: "test test test test test test test test test test test junk",
        path: "m/44'/118'/0'/0",
        initialIndex: 0,
        count: 20,
        passphrase: "",
      },
  }
}
</code></pre>

## Step 2: Contract deployment[​](https://docs.kava.io/docs/ethereum/development#contract-deployment) <a href="#contract-deployment" id="contract-deployment"></a>

`joltevm` directory contains an integrated Hardhat project for smart contract deployment and interaction.

```sh
# Navigate to the joltevm directory
cd joltevm

# Install dependencies
yarn
```

A sample Solidity smart contract located at `joltevm/contracts/token/Token.sol` can be easily deployed with:

```sh
yarn token-deploy
```

Under the hood `yarn token-deploy` is running cmd `npx hardhat run --network testnet scripts/deploy/token.js` to deploy the token on Joltify testnet. You can also use the `npx hardhat ...` syntax to interact with the contracts from the CLI.

## Step 3: Contract interaction[​](https://docs.kava.io/docs/ethereum/development#contract-interaction) <a href="#contract-interaction" id="contract-interaction"></a>

Basic contract interaction is possible through additional scripts with the full list available in `evm/package.json`. Each yarn cmd is aliased to one of the Hardhat tasks found in `hardhat.config.js`.

```sh
# Get the deployed TEST token's total supply
yarn token-totalSupply --token TOKEN_CONTRACT_ADDRESS
```

You can get information about any cmd including param options by using the `--help` flag, for example:

```sh
yarn token-totalSupply --help
```

Using the available scripts, we can transfer tokens:

```sh
yarn token-transfer --token TOKEN_CONTRACT_ADDRESS --recipient RECIPIENT_ADDRESS --amount AMOUNT
```

## Step 4: Customised contract deployment[​](https://docs.kava.io/docs/ethereum/development#deploying-your-own-contracts) <a href="#deploying-your-own-contracts" id="deploying-your-own-contracts"></a>

You can easily deploy your own contracts:

1. Add your Solidity smart contract to `joltevm/contracts`.
2. Add a deployment script to `joltevm/scripts/deploy/[YOUR_SCRIPT].js`.
3. Add Hardhat tasks corresponding to your contract's public methods to the existing tasks in `hardhat.config.js`.

Done! Now you can deploy and interact with your contract:

```sh
# Deployment on Joltify testnet
npx hardhat run --network testnet scripts/deploy/[YOUR_SCRIPT].js

# Interaction on Joltify testnet
npx hardhat [YOUR_TASK_NAME] --network testnet
```

If you want a cleaner CLI experience, you can add some script aliases to `joltevm/package.json` as seen in the `scripts` section.

## Step 5: Verify deployed contract

To verify the deployed contract on joltify EVM explorer, you are required to set up `etherscan` in `hardhat.config.js` file as follows:

```javascript
etherscan: {
    apiKey: {
      testnet: "any_but_not_empty",
    },
    customChains: [
      {
        network: "testnet",
        chainId: 1730,
        urls: {
          apiURL: "https://backend-evm-dev.joltify.io/api/",
          browserURL: "https://evm-explorer-dev.joltify.io/"
        }
      }
    ]
  }
```

Once the configuration is set up, you can run the following command to verify the deployed contract on Joltify EVM explorer:

```sh
npx hardhat verify --network testnet --contract contracts/token/Token.sol:Token DEPLOYED_CONTRACT_ADDRESS "constructor argument 1"
```

or

```
yarn verify --contract contracts/token/Token.sol:Token DEPLOYED_CONTRACT_ADDRESS "constructor argument 1"
```

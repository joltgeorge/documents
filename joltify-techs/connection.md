# Connection

## Table of Content

* [Keplr Wallet Connection](connection.md#keplr-wallet-connection)
* [MetaMask Wallet Connection](connection.md#metamask-wallet-connection)

## Keplr Wallet Connection

### 1. Download the Keplr Wallet Extension

* **Download** the Keplr wallet extension from the Chrome Web Store.

### 2. Setup the Keplr wallet

Users have the following options to set up their accounts in the Keplr wallets.

* Create a new wallet.
* Import an existing wallet.
* Connect Hardware Wallet (Ledger and Keystone are officially supported).
  * [Guidance](https://support.ledger.com/hc/en-us/articles/4411149814417-Set-up-and-use-Keplr-to-access-your-Ledger-Cosmos-Ecosystem-Accounts?docs=true) from Ledger.
  * [Guidance](https://support.keyst.one/3rd-party-wallets/cosmos-wallets/keplr-extension) from Keystone.

### 3. Connect Joltify chain in the Keplr wallet

* **Add  Joltify mainnet chain in the Keplr wallet.**
  * **Visit** the Joltify Mainnet page ([https://app.joltify.io/](https://app.joltify.io/)) and **Approve** the connection in the Keplr Wallet.
* **Add Joltify testnet chain in the Keplr wallet.**
  * **Visit** the Joltify Testnet page  ([https://testnet2.joltify.io/](https://testnet2.joltify.io/)) and **Approve** the connection in the Keplr Wallet.

### 4. Enable chain visibility in the Keplr wallet

1. Go to "Settings" -> "General" -> "Manage Chain Visibility".
2. Search "Joltify Mainnet/Testnet network" by typing `joltify` for mainnet / `JoltDev3` for testnet.
3. Tick the box to enable the visibility of the Joltify mainnet/testnet network in the Keplr wallet.

### 5. Copy your jolt-address

1. Open the Keplr Wallet extension.
2. Click on "Copy Address" button.
3. copy your _jolt-address_.

## MetaMask Wallet Connection

### 1. Download the MetaMask Wallet Extension

* **Download** the Metamask wallet extension from the Chrome Web Store.

### 2. Add accounts in the MetaMask wallet

Users have the following options to set up their accounts in the Keplr wallets.

* Add a new account
* Import account.
* Add hardware wallet (Ledger , Trezor, and Lattice are officially supported).
  * [Tutorial](https://support.ledger.com/hc/en-us/articles/4404366864657-Connect-your-Ledger-to-MetaMask?docs=true) for Ledger.
  * [Tutorial](https://trezor.io/learn/a/metamask-and-trezor) for Trezor.
  * Alternatively, you can connect your QR hardware wallet.

### 3. Connect Joltify EVM chain in the MetaMask wallet

Users can use the following configuration to add Joltify EVM network in the MetaMask wallet.

#### Joltify EVM Mainnet Config

* Chain Name: joltify-evm-mainnet
* RPC URL: [https://evm.joltify.io/](https://evm.joltify.io/)
* Chain ID: 1729
* Currency Symbol: JOLT
* Explorer URL: **To be updated**

#### Joltify Testnet

* Chain Name: joltify-evm-testnet
* RPC URL: [http://65.109.48.184:8555/](http://65.109.48.184:8555/)
* Chain ID: 1730
* Currency Symbol: JOLT
* Explorer URL: [https://evm-explorer-dev.joltify.io](https://evm-explorer-dev.joltify.io/)


# Validating on Joltify

To become a validator, you can use [Joltify installer ](joltify-installer.md)or follow the steps below.

## 1. Run a full node

To become a validator, you must first have `joltify` binary CLI installed and be able to run a full node. You can follow the [guide](running-a-node.md) to setup your full node if you haven't yet.

## 2. Create a validator

In order to create a validator, you need to create a wallet first. This will be used in order to hold the tokens that you will later delegate to your validator node, allowing the validator to properly work.

You can run the following to add key (replace `key_name` with a name of your choice):

```sh
joltify keys add <key_name> --keyring-backend file
```

Alternatively, for securing a validator, we highly recommend using a Ledger device. To create a new wallet with a Ledger (replace `key_name` with a name of your choice), please run:

```sh
joltify keys add <key_name> --keyring-backend file --ledger
```

{% hint style="info" %}
If you encounter any connection problem with Ledger, you can refer to the [Ledger Support](https://support.ledger.com/hc/en-us/articles/115005165269-Fix-USB-connection-issues-with-Ledger-Live?support=true).&#x20;
{% endhint %}

To ensure your wallet was saved to your keyring, you can check the keys list by running the following command:

```sh
joltify keys list --keyring-backend file
```

## 3. Obtain validator public key

The last thing needed before initialising the validator is to obtain your validator public key which was created when you first initialised your node. To obtain your validator `pubkey`:

```
joltify tendermint show-validator
```

## 4. Create validator

Ensure you have a small amount of JOLT on the wallet address you are using on your keyring in order to successfully send a transaction. Once you have a balance on the address on your keyring, you can now send the create-validator transaction.

Here is the empty command:

```shell
joltify tx staking create-validator \
--from=[key_name] \
--amount=[staking_amount_ujolt] \
--pubkey=$(joltify tendermint show-validator) \
--moniker="[moniker_id_of_your_node]" \
--security-contact="[security contact email/contact method]" \
--chain-id="[chain-id]" \
--commission-rate="[commission_rate]" \
--commission-max-rate="[maximum_commission_rate]" \
--commission-max-change-rate="[maximum_rate_of_change_of_commission]" \
--min-self-delegation="[min_self_delegation_amount]" \
--ledger \ # use this flag if you are using ledger
```

Here is the same command but with example values:

```sh
joltify tx staking create-validator \
--from=myKeyName \
--amount=1000jolt \
--pubkey=$(joltify tendermint show-validator) \
--moniker="yourMoniker" \
--security-contact="test@joltify.finance" \
--chain-id="joltifydev_1730-1" \
--commission-rate="0.05" \
--commission-max-rate="0.2" \
--commission-max-change-rate="0.01" \
--min-self-delegation="100000000" \
--ledger \ # use this flag if you are using ledger
```

Here is the explanation of these command flags:

* The `from` flag is the KEY\_NAME you created when initialising the key on your keyring.
* The `amount` flag is the amount you will place in your own validator (Note that the initial staking amount must be greater than `min-self-delegation`).
* The `pubkey` is the validator public key found earlier.
* The `moniker` is a human readable name you choose for your validator.
* The `security-contact` is an email that your delegaters are able to  use to contact you.
* The `chain-id` is whatever chain-id you are working with (in the Joltify Testnet case it is `joltifydev_1730-1`).
* The `commission-rate` is the rate you will charge your delegates (in the example above, 10 percent).
* The `commission-max-rate` is the most you are allowed to charge your delegates (in the example above, 20 percent).
* The `commission-max-change-rate` is how much you can increase your commission rate in a 24 hour period (in the example above, 5 percent per day until reaching the max rate).
* The `min-self-delegation` is the lowest amount of personal funds the validator is required to have in their own validator to stay bonded (in the example above, 100JOLT).
* The `ledger` flag indicates that you are using Ledger.

## 5. Track validator active set

```sh
joltify query staking validators --limit 300 -o json | jq -r '.validators[] |
[.operator_address, .status, (.tokens|tonumber / pow(10; 6)),
.commission.update_time[0:19], .description.moniker] | @csv' | column -t -s","
```

You can search for your specific moniker by adding grep MONIKER at the end:

```sh
joltify query staking validators --limit 300 -o json | jq -r '.validators[] |
[.operator_address, .status, (.tokens|tonumber / pow(10; 6)),
.commission.update_time[0:19], .description.moniker] | @csv' | column -t -s"," | grep brian
```

If your bond status is `BOND_STATUS_BONDED`, congratulations, your validator is part of the active validator set!

## 6. Track validator's signing

To track your validator's signing history, copy the validator public key:

```sh
joltify tendermint show-validator
```

Use your validators public key queried above as the validator-pubkey below:

```shell
joltify query slashing signing-info [validator-pubkey]
```

Example:

```sh
joltify query slashing signing-info '{"@type":"/cosmos.crypto.ed25519.PubKey","key":"WYKlg+xfaxxS+YzpPmwBhbcFtIHD/EfQr/TXoDxXxWQ="}'
```

# Validating On Joltify

## 1. Run a full node

To become a validator, you must first have `joltify` binary CLI installed and be able to run a full node. You can follow the [guide](running-a-node/) to setup your full node if you haven't yet.

## 2. Create a validator

In order to create a validator, you need to create a local wallet first. This will be used in order to hold the tokens that you will later delegate to your validator node, allowing the validator to properly work.&#x20;

In order to create a new wallet (replace `key_name` with a name of your choice), please run:

```sh
joltify keys add <key_name>
```

{% hint style="info" %}
Ensure you write down the mnemonic as you can not recover the wallet without it.&#x20;
{% endhint %}

**Or** you can add an existing wallet through your seed phrase:

```
joltify keys add <key_name> --recover
```

To ensure your wallet was saved to your keyring, the wallet name (i.e., `key_name`) is in your keys list:

```sh
joltify keys list
```

## 3. Obtain validator public key

The last thing needed before initialising the validator is to obtain your validator public key which was created when you first initialised your node. To obtain your validator pubkey:

```
joltify tendermint show-validator
```

## 4. Create Validator Command

Ensure you have a small amount of JOLT on the wallet address you are using on your keyring in order to successfully send a transaction. Once you have a balance on the address on your keyring, you can now send the create-validator transaction.

Here is the empty command:

```shell
joltify tx staking create-validator \
--from=[KEY_NAME] \
--amount=[staking_amount_ujolt] \
--pubkey=$(joltify tendermint show-validator) \
--moniker="[moniker_id_of_your_node]" \
--security-contact="[security contact email/contact method]" \
--chain-id="[chain-id]" \
--commission-rate="[commission_rate]" \
--commission-max-rate="[maximum_commission_rate]" \
--commission-max-change-rate="[maximum_rate_of_change_of_commission]" \
--min-self-delegation="[min_self_delegation_amount]" \
--gas="auto" \
--gas-prices="[gas_price]" \
```

Here is the same command but with example values:

```sh
joltify tx staking create-validator \
--from=operator \
--amount=100000000ujolt \
--pubkey=  \
--moniker="brian" \
--security-contact="brian@joltify.finance" \
--chain-id="joltifydev-1730-1" \
--commission-rate="0.1" \
--commission-max-rate="0.2" \
--commission-max-change-rate="0.05" \
--min-self-delegation="100000000" \
```

Here is the explanation of these command flags:

* the `from` flag is the KEY\_NAME you created when initialising the key on your keyring
* the `amount` flag is the amount you will place in your own validator in `ujolt` (in the above example, 100000000ujolt is 100JOLT )
* the `pubkey` is the validator public key found earlier
* the `moniker` is a human readable name you choose for your validator
* the `security-contact` is an email your delegates are able to contact you at
* the `chain-id` is whatever chain-id you are working with (in the Joltify Testnet case it is joltifydev\_1730-1)
* the `commission-rate` is the rate you will charge your delegates (in the example above, 10 percent)
* the `commission-max-rate` is the most you are allowed to charge your delegates (in the example above, 20 percent)
* the `commission-max-change-rate` is how much you can increase your commission rate in a 24 hour period (in the example above, 5 percent per day until reaching the max rate)
* the `min-self-delegation` is the lowest amount of personal funds the validator is required to have in their own validator to stay bonded (in the example above, 100JOLT)
* the `gas-prices` is the amount of gas used to send this create-validator transaction

## Track Validator Active Set

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

## Track Validator Signing

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

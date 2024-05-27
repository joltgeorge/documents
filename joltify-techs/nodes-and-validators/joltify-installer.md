# Joltify Installer

{% hint style="info" %}
**Requirements**\
Before starting, make sure you read the [requirement](requirements.md) to make sure your hardware meets the necessary requirements.
{% endhint %}

## Installing Environment

* **OS**: **Linux/arm64** or **Linux/amd64**
* Python Version >= 3

## 1. Clone Installer Source Code

<pre class="language-sh"><code class="lang-sh"><strong># 1. go to the home directory
</strong><strong>cd
</strong><strong>
</strong><strong># 2. Clone the installer source code
</strong><strong>git clone https://gitlab.com/joltify/joltify-installer.git
</strong><strong>
</strong><strong># 3. Checkout
</strong><strong>git checkout v0.1.0
</strong></code></pre>

## 2. Install Dependencies

```sh
# 1. go the installer folder
cd joltify-installer

# 2. install the requirements
pip3 install -r requirements.txt
```

## 3. Prepare Config Files

```shell
# Cp Set mainnet config
cp .env.example .env.mainnet
# or Set testnet config
cp .env.example .env.testnet
# as for the configuration details, please refer to the Configuration section 
```

## 4. Start Installing

```sh
# Run the installer
python3 joltify-installer.py
```

## Configuration

You can add these items in the configuration file to override the default settings. Before making any modifications, please make sure you understand the purpose of each item.

### **Local Chain**

```toml
installHome
version
chainID
```

### **Peer Chain**

```toml
# rpc: the rpc address of the peer chain
rpc
# api: the rest address of the peer chain
api
# peers: the p2p address of the peer chain
# mainnet peers: "1063f14d45345b3ed0c0e533bda52e78de754d24@37.27.18.236:26656,34e0148645d860f45db510981f15da94b6b9d6ce@168.119.171.59:26656,2dd612a8c7268da6685c612f956eba2f42ae06f3@64.176.42.11:26656"
peers
```

### **Ports**

```toml
# parameters in `app.toml` file
apiServer
grpcServer
grpcWebServer

# parameters in `config.toml` file
abciApp
rpcLAddr
p2pLAddr
pprofLAddr
```

### **Pruning**

```toml
# default: the last 362880 states are kept, pruning at 10 block intervals
# nothing: all historic states will be saved, nothing will be deleted (i.e. archiving node)
# everything: 2 latest states will be kept; pruning at 10 block intervals.
# custom: allow pruning options to be manually specified through 'pruning-keep-recent', and 'pruning-interval'
pruning
pruningKeepRecent
pruningInterval
```

### **Node**

```toml
# full: Full Node (download chain data and run locally)
# client: Client Node (setup a daemon and query a public RPC)
nodeType
# desired node name, cant be blank
nodeName
# Use swap if less than 32GB RAM are detected
# true or false
swapOn
# There have been reports of replay from genesis needing extra swap (up to 64GB) to prevent OOM errors.
# Use swap if less than 64GB RAM are detected
# true or false
extraSwapOn
# Data Sync Selection
# genesis: Start at block 1 and automatically upgrade at upgrade heights (replay from genesis)
# exit: Only install the daemon
dataSync
# Replaying Setup
# true: start cosmovisor as a background service and replay now
# false: do not start replay now
replayNow
```

### **Validator**

```toml
# The name of your validator and this can not be blank
validatorMoniker
# The validator's (optional) security contact email
securityContact
# The validator's (optional) details
details
# The optional identity signature (ex. UPort or Keybase)
identity
# The validator's (optional) website
website
# the staking amount currently being self delegated to the validator
stakeAmount
# the commission rate charged to delegators
commissionRate
# the maximum rate the commission can be set to
commissionMaxRate
# the maximum rate the commission can be changed in one change
commissionMaxChangeRate
# the minimum self delegation required to be a validator
minSelfDelegation
```

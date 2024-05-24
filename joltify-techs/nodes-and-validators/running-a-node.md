# Running a Fullnode

A full node is a program that fully validates transactions and blocks of a blockchain. It is distinct from a light node that only processes block headers and a small subset of transactions. Running a full-node requires more resources than a light-node but is necessary in order to be a validator. In practice, running a full-node only implies running a non-compromised and up-to-date version of the software with low network latency and without downtime.

It is encouraged for users to run full-nodes even if they do not plan to be validators.

You can join Joltify network as a node by using Joltify installer.

{% content-ref url="joltify-installer.md" %}
[joltify-installer.md](joltify-installer.md)
{% endcontent-ref %}

Alternatively, you can manually install a full node by following steps below.&#x20;

## Manually install a full node

{% hint style="info" %}
**Requirements**\
Before starting, make sure you read the [requirement](requirements.md) to make sure your hardware meets the necessary requirements.
{% endhint %}

### 1. Setup your environment

In order to run a full node, you need to build `joltify` which requires `Go`, `git`, `gcc`, `make`, and `supervisor` installed.

This process depends on your working environments. We support:

* Linux/arm64
* Linux/amd64

The following example is based on **Ubuntu (Debian)** and assumes you are using a terminal environment by default. Please run the equivalent commands if you are running other Linux distributions.&#x20;

```sh
# 1. Update the system
sudo apt update
```

```sh
# 2. Install git, gcc, make, and supervisor
apt install git build-essential ufw curl jq snapd supervisor -y
```

```sh
# 3.1 Install go with go-installer
wget https://git.io/go-installer.sh && bash go-installer.sh --version 1.21.1

# source the .bashrc file
source ~/.bashrc

# 3.2 Check go version
go version
```

### 2. Build the software

In your terminal, you can run the following to build `joltify` binary CLI:&#x20;

<pre class="language-sh"><code class="lang-sh"><strong># Make sure you are in the HOME directory
</strong><strong>cd $HOME
</strong><strong>
</strong><strong># Clone the joltify software
</strong>git clone https://gitlab.com/joltify/joltify_lending

# Go to the source code folder
cd joltify_lending

# Checkout the correct tag to the expected versi
git checkout tags/v0.x.x

# Build the software
make install
</code></pre>

If the software is built successfully, the `joltify` executable will be located inside your `$GOBIN` path. To check this, try running:

```sh
joltify version --long

# example output:
...
commit: xxx
cosmos_sdk_version: v0.47.4
go: go version go1.21.1 linux/arm64
name: joltify
server_name: joltify
version: 0.x.x
```

### 3. Initialize the working directory

Configuration files and chain data will be stored inside the `$HOME/.jolityf` directory by default. In order to create this folder and all the necessary data we need to initialize a new full node using the `joltify init` command.

Run the `init` command to init:

```sh
joltify init <node_name> --chain-id <chain_id> --home $HOME/.joltify
```

{% hint style="info" %}
You can choose any `node_name` value you like. It is a public name for the node such as 'my\_joltify\_node', and will be saved in the `config.toml` under the `$HOME/.joltify/` working directory.
{% endhint %}

### 4. GET the genesis file

To connect to an existing network, or start a new one, a genesis file is required. The file contains all the settings telling how the genesis block of the network should look like.

Download and place the genesis file in the joltify config folder (replace `RPC_URL` with the valid joltify rpc url):

```sh
curl <rpc_url>/genesis |jq '.result''.genesis'>$HOME/.joltify/config/genesis.json
```

### 5. Setup seeds

Next, you'll need to tell your node how to connect with other nodes that are already present on the network. In order to do so, you will use the `seeds` and `persistent_peers` values of the `$HOME/.joltify/config/config.toml` file.

A seed node is a node who relays the addresses of other peers which they know of. These nodes constantly crawl the network to try to get more peers. The addresses which the seed node relays get saved into a local address book. Once these are in the address book, you will connect to those addresses directly.

Run the following to place the seed info in the config.toml file (replace `peer_address` with the valid peer address):

```sh
sed -i -E 's|persistent_peers = \"\"|persistent_peers = \"<peer_address>\"|g' $HOME/.joltify/config/config.toml
```

You can use the following `peer_address` to run a node on Joltify mainnet:

```toml
peer_address = "1063f14d45345b3ed0c0e533bda52e78de754d24@37.27.18.236:26656,34e0148645d860f45db510981f15da94b6b9d6ce@168.119.171.59:26656,2dd612a8c7268da6685c612f956eba2f42ae06f3@64.176.42.11:26656"
```

### 6. Setup cosmovisor

The Cosmos team provides a tool named _Cosmovisor_ that allows your node to perform some automatic operations when needed. This is particularly useful when dealing with on-chain upgrades, because Cosmovisor can help you by taking care of downloading the updated binary and restarting the node for you.

### 6.1 Install cosmovisor

Set up Cosmovisor to ensure any future upgrades happen flawlessly. To install Cosmovisor:

```sh
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

(You may also refer to the Cosmovisor [installation instructions](https://github.com/cosmos/cosmos-sdk/tree/main/tools/cosmovisor#installation).)

### 6.2 Create the required directories

```sh
mkdir -p $HOME/.joltify/cosmovisor
mkdir -p $HOME/.joltify/cosmovisor/genesis
mkdir -p $HOME/.joltify/cosmovisor/genesis/bin
mkdir -p $HOME/.joltify/cosmovisor/upgrades
```

### 6.3 Set the environment variables

```sh
echo "# Setup Cosmovisor" >> $HOME/.profile
echo "export DAEMON_NAME=joltify" >> $HOME/.profile
echo "export DAEMON_HOME=$HOME/.joltify" >> $HOME/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> $HOME/.profile
echo "export DAEMON_LOG_BUFFER_SIZE=512" >> $HOME/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> $HOME/.profile
echo "export UNSAFE_SKIP_BACKUP=true" >> $HOME/.profile
source $HOME/.profile
```

You may leave out `UNSAFE_SKIP_BACKUP=true`, however the backup takes a decent amount of time and public snapshots of old states are available.

### 6.4 Copy the current joltify binary into the cosmovisor/genesis folder:

```sh
cp $GOPATH/bin/joltify ~/.joltify/cosmovisor/genesis/bin
```

### 6.5 Check binary version

Ensure the version of cosmovisor and joltify are the same:

```
cosmovisor version
joltify version
```

### 7. Start cosmovisor service

### 7.1 Construct comosvisor conf file

Construct the `cosmovisor.conf` file for starting the cosmoviso service (replace `user_name`, `GOPATH` and `HOME` with your choice):

```sh
echo '[program:cosmovisor]
environment=
    DAEMON_NAME="joltify",
    DAEMON_HOME="$HOME/.joltify",
    DAEMON_RESTART_AFTER_UPGRADE=true,
    DAEMON_ALLOW_DOWNLOAD_BINARIES=true,
    DAEMON_LOG_BUFFER_SIZE=512,
    UNSAFE_SKIP_BACKUP=true,
    HOME="$HOME/.joltify",

command = $GOPATH/bin/cosmovisor run start --home $HOME/.joltify
user = <user_name>

autostart = true
autorestart = false
startsecs = 3
stopwaitsecs = 300

stdout_logfile = /var/log/cosmovisor.log
redirect_stderr = true
stdout_logfile_maxbytes = 200MB
stdout_logfile_backups = 5
' > cosmovisor.conf
```

Move the `cosmovisor.conf` file to the supervisor conf folder:

```sh
mv cosmovisor.conf /etc/supervisor/conf.d/
```

### 7.2 Start supervisord

Run the following to start cosmovisor via supervisor:

```sh
# 1. Start supervisord
supervisord -c /etc/supervisor/supervisord.conf

# 2. Read the cosmosvior config file
supervisorctl reread

# 3. Add cosmovisor
supervisorctl add

# 4. Start cosmovisor by updating the process in supervisor
supervisorctl update

# 5. Check the status of cosmosvisor
supervisorctl status cosmovisor
```

### 8. Check node syncing progress

Once you start the `cosmovisor` via supervisor successfully, you can track your the sync progress of your full node:

```sh
# 1. View the log
tail -f /var/log/cosmovisor.log
```

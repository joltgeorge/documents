# Manually Installing

## Prerequisite

* OS System: Linux
* Python Version: >= 3

## Step 0: Upgrade & Install Dependencies

### (optional) Linux Update

```sh
apt-get update
```

### (optional) Necessary Tool List

```sh
apt install git build-essential ufw curl jq snapd supervisor -y
```

### Start Supervisord

Ensure that the Supervisord is started.

```sh
supervisord -c /etc/supervisor/supervisord.conf 
```

### Go Requirement

You will need to be running go1.21 for this version of Joltify. You can check if you are running go1.21 with the following command:

```sh
go version
```

If this does not say go 1.21.1, you need to upgrade/downgrade. One of the many ways to upgrade/downgrade to/from go 1.21.1 on linux is as follows:

```sh
wget https://git.io/go-installer.sh && bash go-installer.sh --version 1.21.1
```

```sh
source ~/.bashrc
```

## Step 1: Install Joltify Binary manually

### Clone Source Code

```sh
git clone https://gitlab.com/joltify/joltify_lending
cd joltify_lending
# switch to the expected versi
git checkout VERSION
# install joltify binary
make install
# check your joltify version
joltify version
```

Make sure you have installed the Joltify Binary CLI prior to following the below instructions.

## Step 2: Initialise Joltify Node

Use joltify  binary CLI to initialise your node (replace the `NODE_NAME` with a name of your choice):

```sh
joltify init NODE_NAME --chain-id CHAIN_ID --home $HOME/.joltify
```

Download and place the genesis file in the joltify config folder (replace `RPC_URL` with the valid joltify rpc url):

```sh
curl RPC_URL/genesis |jq '.result''.genesis'>$HOME/.joltify/config/genesis.json
```

Place the seed info in the config.toml file (replace `PEER_ADDRESS` with the valid peer address):

e.g., PEER\_ADDRESS = 545e0121ef3cc8fb7bf319a8ec62657272c0618e@67.219.103.128:26656

```sh
sed -i -E 's|persistent_peers = \"\"|persistent_peers = \"PEER_ADDRESS\"|g' $HOME/.joltify/config/config.toml 
```

Replace the token denom in the app.toml file:

```sh
sed -i -E 's|0stake|0ujolt|g' $HOME/.joltify/config/app.toml
```

## Step 3: Set Up Cosmovisor

### Install Cosmovisor

Set up Cosmovisor to ensure any future upgrades happen flawlessly. To install Cosmovisor:

```sh
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

(You may also refer to the Cosmovisor [installation instructions](https://github.com/cosmos/cosmos-sdk/tree/main/tools/cosmovisor#installation).)

### Create the required directories

```sh
mkdir -p $HOME/.joltify/cosmovisor
mkdir -p $HOME/.joltify/cosmovisor/genesis
mkdir -p $HOME/.joltify/cosmovisor/genesis/bin
mkdir -p $HOME/.joltify/cosmovisor/upgrades
```

### Set the environment variables

```sh
echo "# Setup Cosmovisor" >> $HOME/.profile
echo "export DAEMON_NAME=joltify" >> $HOME/.profile
echo "export DAEMON_HOME=$HOME/.joltify" >> $HOME/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> $HOME/.profile
echo "export DAEMON_LOG_BUFFER_SIZE=512" >> $HOME/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> $HOME/.profile
echo "export UNSAFE_SKIP_BACKUP=true" >> $HOME/.profile
source ~/.profile
```

You may leave out `UNSAFE_SKIP_BACKUP=true`, however the backup takes a decent amount of time and public snapshots of old states are available.

### Copy the current joltify binary into the cosmovisor/genesis folder:

```
cp $GOPATH/bin/joltify ~/.joltify/cosmovisor/genesis/bin
```

### Check binary version

Ensure the version of cosmovisor and joltify are the same:

```
cosmovisor version
joltify version
```

## Step 4: Start Cosmovisor Service

### Construct comosvisor conf file

Construct the cosmovisor.conf file for starting the cosmoviso service (replace `USER_NAME`, `GOPATH` and `HOME` with your choice):

```sh
echo '[program:cosmovisor]
environment=
    DAEMON_NAME="joltify",
    DAEMON_HOME="/root/.joltify",
    DAEMON_RESTART_AFTER_UPGRADE=true,
    DAEMON_ALLOW_DOWNLOAD_BINARIES=true,
    DAEMON_LOG_BUFFER_SIZE=512,
    UNSAFE_SKIP_BACKUP=true,
    HOME="HOME/.joltify",

command = GOPATH/bin/cosmovisor run start --home HOME/.joltify
user = USER_NAME

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

Move the cosmovisor.conf file to the supervisor conf folder:

```sh
mv cosmovisor.conf /etc/supervisor/conf.d/
```

Load the cosmovisor conf file in supervisord:

```sh
supervisoctl reread
supervisorctl add
supervisorctl update
```

## Step 5: Check Node Sync Progress

```sh
tail -f /var/log/cosmovisor.log
```

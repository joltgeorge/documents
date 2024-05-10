# Joltify Installer

## Prerequisite

* OS System: Linux
* Python Version: >= 3

## Step 1: Clone Installer Source Code

```sh
git clone https://gitlab.com/joltify/joltify-installer.git
```

## Step 2: Install Dependencies

```
cd joltify-installer
pip3 install -r requirements.txt
```

## Step 3: Prepare Config Files

```shell
# mainnet config
cp .env.example .env.mainnet
# or testnet config
cp .env.example .env.testnet
```

## Step 4: Start Installing

```
python3 joltify-installer.py
```




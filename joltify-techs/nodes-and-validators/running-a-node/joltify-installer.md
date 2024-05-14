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
# Set mainnet config
cp .env.example .env.mainnet

# or Set testnet config
cp .env.example .env.testnet
```

## 4. Start Installing

```sh
# Run the installer
python3 joltify-installer.py
```

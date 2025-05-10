# Aztec Network – Run a Sequencer Node (Testnet)

A step-by-step guide to run a **Sequencer Node** on the Aztec testnet and earn the **Apprentice** role.

* Funding : $119M


---


# Join Crypto Console Community

Join TG : [Crypto Console Telegram](https://t.me/cryptoconsol) 

Follow X : [Crypto Console Twitter](https://www.x.com/cryptoconsol) 

Subscribe : [Crypto Console Youtube](https://www.youtube.com/@cryptoconsole)


---

# VPS Options

💻 Contabo VPS Deals 🚀 Buy with Credit Card/Paypal/Crypto Credit card : 

Get powerful VPS solutions with these direct links:  

VPS 4C : [contabo VPS 4C](https://www.kqzyfj.com/click-101278318-13796470)

VPS 6C : [Contabo VPS 6C](https://www.kqzyfj.com/click-101278318-13796472)

VPS 8C : [Contabo VPS 8C](https://www.jdoqocy.com/click-101278318-13796474)

VPS 10C : [Contabo VPS 10C](https://www.anrdoezrs.net/click-101278318-13796476)

VPS 14C : [contabo VPS 14C](https://www.kqzyfj.com/click-101278318-15807107)

---

## Hardware requirements:

| **Hardware** | **Minimum Requirement** |
|--------------|-------------------------|
| **CPU**      | 8 Cores                 |
| **RAM**      | 16 GB                    | 
| **Disk**     | 100 GB  SSD              |

---

## 1. Install Dependencies

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt install curl iptables screen build-essential make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev git wget lz4 jq libleveldb-dev tar clang bsdmainutils ncdu unzip
```

---

## 2. Install Docker & Docker compose

* Docker 
```
sudo apt install docker.io
docker --version
```
* Docker-Compose

```bash
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/bin/docker-compose && \
sudo chmod +x /usr/bin/docker-compose && \
sudo docker-compose --version
```

---

## 3. Install Aztec CLI

```bash
bash -i <(curl -s https://install.aztec.network)
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
* Restart Terminal

```
aztec
```

---

## 4. Update Aztec

```bash
aztec-up alpha-testnet
```

---

## 5. Set Up RPC & Beacon URLs

Use:

* **RPC URL**: [Alchemy](https://dashboard.alchemy.com/)
* **Beacon URL**: [drpc](https://drpc.org/)

---

## 6. Get Ethereum Keys

Generate an EVM wallet. Save both:

* Public Address
* Private Key

---

## 7. Get Sepolia ETH

Use a faucet to fund your wallet with Sepolia ETH.

* google faucet : https://cloud.google.com/application/web3/faucet
* Alchemy faucet : https://www.alchemy.com/faucets/ethereum-sepolia

---

## 8. Find Public IP

```bash
hostname -I
```

---

## 9. Configure Firewall

```bash
ufw allow 22
ufw allow ssh
ufw enable
ufw allow 40400
ufw allow 8080
```

---

## 10. Run Sequencer Node
```
cd .aztec && \
nano docker-compose.yml
```
* Paste below
```
version: '3.8'

services:
  node:
    image: aztecprotocol/aztec:0.85.0-alpha-testnet.8
    environment:
      ETHEREUM_HOSTS: ""
      L1_CONSENSUS_HOST_URLS: "" 
      DATA_DIRECTORY: /data
      VALIDATOR_PRIVATE_KEY: "<YOUR_PRIVATE_KEY>"
      P2P_IP: "<IP_ADDRESS>"
      LOG_LEVEL: debug
    entrypoint: >
      sh -c 'node --no-warnings /usr/src/yarn-project/aztec/dest/bin/index.js start --network alpha-testnet start --node --archiver --sequencer'
    ports:
      - 40400:40400/tcp
      - 40400:40400/udp
      - 8080:8080
    volumes:
      - /root/.aztec/data:/data
```

**Repalace respectively**


* Start your docker-compose file:
```
docker-compose up -d
```
* Check logs:
```
docker-compose logs -f
```

Wait for your node to synchronize.

## 11. Get 'Apprentice' Role

### Step 1: Get Block Number

```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r ".result.proven.number"
```

### Step 2: Generate Sync Proof

```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params:["BLOCK_NUMBER","BLOCK_NUMBER"],"id":67}' \
http://localhost:8080 | jq -r ".result"
```

**Replace your Block number on ["BLOCK_NUMBER","BLOCK_NUMBER"] ex : if your block number from first command is 234124 , then ["234124","234124"]**

### Step 3: Register via Discord

Go to `#operators | start-here` on Aztec’s [Discord](https://discord.gg/aztec) and use the `/operator start` command with:

* `address`: Your wallet address
* `block-number`: From Step 1
* `proof`: From Step 2

---

## 12. Register Validator

```bash
aztec add-l1-validator \
  --l1-rpc-urls YOUR_RPC_URL \
  --private-key YOUR_PRIVATE_KEY \
  --attester YOUR_PUBLIC_ADDRESS \
  --proposer-eoa YOUR_PUBLIC_ADDRESS \
  --staking-asset-handler 0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2 \
  --l1-chain-id 11155111
```
* Replace values respectively 
* For validator registration need to have atleast 2.5 ETH on sepolia
---

## 13. Check Node Status

### Get Peer ID:

```bash
sudo docker logs $(docker ps -q --filter ancestor=aztecprotocol/aztec:alpha-testnet | head -n 1) 2>&1 | grep -i "peerId" | grep -o '"peerId":"[^"]*"' | cut -d'"' -f4 | head -n 1
```

* Use [Aztec Nethermind Explorer](https://aztec.nethermind.io/) to search your Peer ID.
* Use [Aztec Scan](https://aztecscan.xyz/validators) to check validator stats.

---

## 14. Update Node
* stop first
```bash
cd .aztec && \
docker-compose down && \
rm -rf data
```

* Change image name
```
nano docker-compose.yml
```

* Run node
```
docker-compose up -d
```

---

Follow : https://x.com/cryptoconsol


# Drosera Network Testnet Contribution Guide

**Drosera** is an innovative Ethereum-aligned security automation layer. They recently raised **$4.75 million** led by Greenfield Capital — and now, you can be part of their testnet journey!

This guide will walk you through every step — from installing dependencies, deploying a trap, to running your own operator node.

---

## Recommended System Requirements
- **4 GB RAM**
- **2 CPU Cores**
- **20 GB Storage**

---

## Overview
We’ll contribute to the Drosera testnet by:
- Deploying a **Trap** on the testnet
- Connecting an **Operator** to the trap

---

## 1. Install Dependencies

```bash
sudo apt-get update && sudo apt-get upgrade -y

sudo apt install curl ufw iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

---

## 2. Install Drosera CLI

```bash
curl -L https://app.drosera.io/install | bash
```

Add Drosera CLI to your current shell session:

```bash
source /root/.bashrc
```

Then install:

```bash
droseraup
```

---

## 3. Install Foundry CLI

```bash
curl -L https://foundry.paradigm.xyz | bash
source /root/.bashrc
foundryup
```

---

## 4. Create a Trap Project

```bash
mkdir my-drosera-trap
cd my-drosera-trap
```

Configure Git:

```bash
git config --global user.email "anthonyugwuja.dev@gmail.com"
git config --global user.name "Tony-smile"
```

Initialize trap:

```bash
forge init -t drosera-network/trap-foundry-template
```

---

## 5. Compile Trap

```bash
curl -fsSL https://bun.sh/install | bash
bun install
forge build
```

---

## 6. Deploy the Trap

```bash
DROSERA_PRIVATE_KEY=your_wallet_private_key drosera apply
```

---

## 7. Verify on Drosera Dashboard

- Visit: https://app.drosera.io
- Connect wallet
- Click “Traps Owned”
- Click your creator account > “Send Bloom Boost” > Deposit Holesky ETH

---

## 8. Fetch Blocks

```bash
drosera dryrun
```

---

## 9. Edit Trap Configuration

```bash
cd my-drosera-trap
nano drosera.toml
```

Append:

```toml
private_trap = true
whitelist = ["your_operator_address"]
```

Apply again:

```bash
DROSERA_PRIVATE_KEY=your_wallet_private_key drosera apply
```

---

## 10. Install Operator CLI

```bash
cd ~
curl -LO https://github.com/drosera-network/releases/releases/download/v1.16.2/drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
tar -xvf drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
sudo cp drosera-operator /usr/bin
```

Check version:

```bash
drosera-operator --version
```

---

## 11. Install Docker

```bash
curl -fsSL https://get.docker.com | sudo bash
sudo usermod -aG docker $USER && newgrp docker
docker run hello-world
```

---

## 12. Pull Docker Image

```bash
docker pull ghcr.io/drosera-network/drosera-operator:latest
```

---

## 13. Register Operator

```bash
drosera-operator register --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com --eth-private-key your_wallet_private_key
```

---

## 14. Create Systemd Service

```bash
sudo tee /etc/systemd/system/drosera.service > /dev/null <<EOF
[Unit]
Description=Drosera Operator Service
After=network-online.target

[Service]
User=$USER
Restart=always
RestartSec=15
LimitNOFILE=65535
ExecStart=$(which drosera-operator) node --db-file-path $HOME/.drosera.db --network-p2p-port 31313 --server-port 31314 \
    --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com \
    --eth-backup-rpc-url https://1rpc.io/holesky \
    --drosera-address 0xea08f7d533C2b9A62F40D5326214f39a8E3A32F8 \
    --eth-private-key your_wallet_private_key \
    --listen-address 0.0.0.0 \
    --network-external-p2p-address your_vps_ip \
    --disable-dnr-confirmation true

[Install]
WantedBy=multi-user.target
EOF
```

---

## 15. Configure Firewall

```bash
sudo ufw allow ssh
sudo ufw allow 22
sudo ufw allow 31313/tcp
sudo ufw allow 31314/tcp
sudo ufw enable -y
```

---

## 16. Start Operator

```bash
sudo systemctl daemon-reload
sudo systemctl enable drosera
sudo systemctl start drosera
```

---

## 17. Check Logs

```bash
journalctl -u drosera.service -f
```

---

## 18. Finalize on Dashboard

- Visit https://app.drosera.io
- Click your creator account > “Send Bloom Boost” > Deposit Holesky ETH > Opt in

---

## Operator Management

Stop:

```bash
sudo systemctl stop drosera
```

Restart:

```bash
sudo systemctl restart drosera
```

---

**GitHub:** [Tony-smile](https://github.com/Tony-smile) 
**Happy Contributing!**

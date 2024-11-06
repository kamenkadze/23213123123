**How to Deploy the Node and Start Mining**

1.** Wallet Configuration**
We recommend using MetaMask or any other EVM-compatible wallet.

> Network Settings
> Network Name: Liberty Project testnet
> RPC URL: https://rpc.libertyproject.space
> Chain ID: 21102
> Currency Symbol: tLBRT
> Block Explorer URL: https://explorer.libertyproject.space

Simply input these settings in your wallet to connect seamlessly with the Liberty network.

2. **Create a Dedicated User**

Create a new user **liberty** for running the node:
`sudo useradd -m -s /bin/bash liberty`

3. **Create a Directory to Store Blockchain Data**

Create a directory in the home folder of the **liberty** user:

`sudo mkdir -p /home/liberty/liberty`

`sudo chown liberty:liberty /home/liberty/liberty`

4. **Download the Node Software**

Download the geth binary to /usr/local/bin and make it executable:

`sudo curl -L https://github.com/LibertyProject-chain/Liberty-Project-testnet-phase-2/releases/download/v0.23/geth-linux-amd64 -o /usr/local/bin/geth`

`sudo chmod +x /usr/local/bin/geth`

5. **Download and Initialize the Genesis File**

Download the genesis file to the liberty user's directory:

`sudo curl -L https://github.com/LibertyProject-chain/Liberty-Project-testnet-phase-2/releases/download/v0.23/genesis.json -o /home/liberty/liberty/genesis.json`

`sudo chown liberty:liberty /home/liberty/liberty/genesis.json`

Initialize the node as the liberty user:

`sudo -u liberty geth --datadir /home/liberty/liberty init /home/liberty/liberty/genesis.json`

6. **Create a Systemd Service File**

Create the file /etc/systemd/system/liberty-node.service with the following content:

`sudo nano /etc/systemd/system/liberty-node.service`

Insert the following content:

> [Unit]
> Description=Liberty Node
> After=network.target
> 
> [Service]
> User=liberty
> Restart=on-failure
> RestartSec=10
> ExecStart=/usr/local/bin/geth --datadir /home/liberty/liberty \
> --networkid 21102 \
> --port 40404 \
> --http.api debug,web3,eth,txpool,net \
> --mine \
> --miner.threads 1 \
> --miner.etherbase <miner address> \
> --gcmode archive \
> --bootnodes "enode://8b7cf2ff6d30e7fe7f1b8bfd67193844504144cb002f1a369326d8cd16227f2c2a0a73ee0e658dac2663b92e1af7e2fbae8a46388dfd5db602f704ee56ca8d57@94.142.138.78:40404"
> LimitNOFILE=4096
> 
> [Install]
> WantedBy=default.target

Note: *Replace <miner address> with your miner's Ethereum address.*

7. **Enable and Start the Service**

Reload systemd to recognize the new service:
`sudo systemctl daemon-reload`

Enable and start the node service:
`sudo systemctl enable liberty-node`

`sudo systemctl start liberty-node`

8. **Check Status and Logs**

To check the status of the node:

`sudo systemctl status liberty-node`

To view the logs:

`sudo journalctl -u liberty-node -f --no-hostname -o cat`

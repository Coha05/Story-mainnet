# Install Homer

## ![Install](https://img.shields.io/badge/Net_work-Story_Homer-red)

### 1. Install dependencies
```
sudo apt update
sudo apt-get update
sudo apt install curl git make jq build-essential gcc unzip wget lz4 aria2 -y
```
### 2. Install Go (Skip if you already installed)
```
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```
### 3. Download Binaries
#### Set Up Story-Geth v1.0.1
```
cd $HOME
git clone https://github.com/piplabs/story-geth
cd story-geth
git checkout v1.0.1
make geth
cp build/bin/geth $HOME/go/bin/story-geth
source $HOME/.bash_profile
story-geth version
```
#### Set Up Story Client v1.1.0
```
cd $HOME
rm -rf story-linux-amd64
wget https://github.com/piplabs/story/releases/download/v1.1.0/story-linux-amd64
[ ! -d "$HOME/go/bin" ] && mkdir -p $HOME/go/bin
if ! grep -q "$HOME/go/bin" $HOME/.bash_profile; then
  echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
fi
chmod +x story-linux-amd64
sudo cp $HOME/story-linux-amd64 $HOME/go/bin/story
source $HOME/.bash_profile
story version
```
### 4. Initialize the node
```
story init --network story --moniker "Your_moniker_name"
```
### 5. Create service file
#### Story-geth service
```
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth Client
After=network.target

[Service]
User=root
ExecStart=/root/go/bin/story-geth --story --syncmode full
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
#### Story Client Service
```
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Consensus Client
After=network.target

[Service]
User=root
ExecStart=/root/go/bin/story run
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
### 6. Load and Start both services
#### Story-geth
```
sudo systemctl daemon-reload && \
sudo systemctl start story-geth && \
sudo systemctl enable story-geth && \
sudo systemctl status story-geth
```
#### Story Client
```
sudo systemctl daemon-reload && \
sudo systemctl start story && \
sudo systemctl enable story && \
sudo systemctl status story
```
### 7. Check logs
Story-geth:
```
sudo journalctl -u story-geth -f -o cat
```
Story Client
```
sudo journalctl -u story -f -o cat
```

### 8. Check the sync status 
```
curl localhost:26657/status | jq
```

### 9. Consider use snapshot here for syncing faster

[CLICK HERE](https://spidernode.net/docs/story/snapshot)

## Create Validator
**Create/Register validator:**
```
story validator create --stake 1024000000000000000000 --moniker "your-node-name" --chain-id 1514 --unlocked=false --private-key "your-evm-priv-key"
```

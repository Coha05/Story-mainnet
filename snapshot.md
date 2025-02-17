# Snapshot
### Snapshot Info
**- Latest Snapshot**: [Block height](https://snapshot.spidernode.net/block_height.txt)

**- Snapshot will auto update every day once**
### Install Required Tools
```
sudo apt-get install wget lz4 aria2 pv -y
```
### Stop Node Services
```
sudo systemctl stop story
sudo systemctl stop story-geth
```
### Download Geth Snapshot
```
cd $HOME
aria2c -x 16 -s 16 https://snapshot.spidernode.net/Geth_snapshot.lz4
```
### Download Story Snapshot
```
cd $HOME
aria2c -x 16 -s 16 https://snapshot.spidernode.net/Story_snapshot.lz4
```
### Backup `priv_validator_state.json`
```
mv $HOME/.story/story/data/priv_validator_state.json $HOME/.story/priv_validator_state.json.backup
```
### Remove Old Data
```
rm -rf ~/.story/story/data
rm -rf ~/.story/geth/story/geth/chaindata
```
### Extract Story and Story-geth Snapshot
```
sudo mkdir -p /root/.story/story/data
lz4 -d Story_snapshot.lz4 | pv | sudo tar xv -C /root/.story/story/
```
```
sudo mkdir -p /root/.story/geth/story/geth/chaindata
lz4 -d Geth_snapshot.lz4 | pv | sudo tar xv -C /root/.story/geth/story/geth/
```
### Restore `priv_validator_state.json`
```
mv $HOME/.story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
```
### Restart Node Services
```
sudo systemctl start story
sudo systemctl start story-geth
```
### Remove snapshot file
```
sudo rm -rf Story_snapshot.lz4
sudo rm -rf Geth_snapshot.lz4
```

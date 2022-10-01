# Installation of the BSC full node

## Creating a dedicated user

```bash
adduser bscnode
usermod -aG sudo bscnode
```

## Installing go programming language

```bash
apt install -y build-essential
wget https://golang.org/dl/go1.16.4.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.4.linux-amd64.tar.gz
# Add go language to PATH
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
```

After reloading a shell, execute :
```bash
go version
```

We should see something like `go version go1.16.4 linux/amd64`

## Downloading a recent snapshot of BSC

```bash
# Download the (big) file
wget --no-check-certificate --no-proxy 'https://s3.ap-northeast-1.amazonaws.com/dex-bin.bnbstatic.com/geth-20210515.zip?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Expires=1623905351&Signature=w1hPMeDxB68aJ2qUM74YbUufCPo%3D' -O geth-20210515.zip
# Check integrity of this file, here it must be equal to the hash indicated by the website
# This can take several hours to complete and the result should be '39e311c37a9844b4dd7fb218553cc99f *geth-20210515.zip'
md5sum -b geth-20210515.zip
# Its always good to make a copy of a big file
cp geth-20210515.zip /root
# Now unzip the file in the node folder
unzip geth-20210515.zip -d node/geth/
```

## Downloading geth

```bash
wget https://github.com/binance-chain/bsc/releases/download/v1.1.0-beta/geth_linux -O geth
chmod +x geth
```

## Downloading config files of BSC Mainnet

```bash
wget https://github.com/binance-chain/bsc/releases/download/v1.1.0-beta/mainnet.zip
unzip mainnet.zip
```

## Files tree

We now have a this file organization :

- bscfullnode
  - config.toml
  - genesis.json
  - geth (executable)
  - node
    - geth
      - data-seed

## Start the synchronization

From `bscfullnode` folder :

```sh
# Initializing the geth database
./geth --datadir ./node init genesis.json
# Complete the config.toml file
./geth --config ./config.toml --cache 18000 --rpc.allow-unprotected-txs --txlookuplimit 0  --syncmode snap dumpconfig > config.toml
# Start syncing the node
./geth --config ./config.toml --datadir ./node --syncmode snap
```

TEST :
./geth --datadir ./node init genesis.json
./geth --config ./config.toml --datadir ./node --syncmode snap
./geth attach node/geth.ipc

```js
var lastPercentage = 0;var lastBlocksToGo = 0;var timeInterval = 10000;
setInterval(function(){
    var percentage = eth.syncing.currentBlock/eth.syncing.highestBlock*100;
    var percentagePerTime = percentage - lastPercentage;
    var blocksToGo = eth.syncing.highestBlock - eth.syncing.currentBlock;
    var bps = (lastBlocksToGo - blocksToGo) / (timeInterval / 1000)
    var etas = 100 / percentagePerTime * (timeInterval / 1000)

    var etaM = parseInt(etas/60,10);
    console.log(parseInt(percentage,10)+'% ETA: '+etaM+' minutes @ '+bps+'bps');

    lastPercentage = percentage;lastBlocksToGo = blocksToGo;
},timeInterval);
```

./geth --config ./config.toml --datadir ./node --cache 18000 --rpc.allow-unprotected-txs --txlookuplimit 0  --syncmode fast

# References

https://docs.binance.org/smart-chain/developer/fullnode.html
https://docs.binance.org/smart-chain/developer/snapshot.html
https://golang.org/doc/install

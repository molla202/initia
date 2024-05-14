<h1 align="center"> INITIA


![image](https://github.com/molla202/pokemon/assets/91562185/c0d15ba5-72dd-4dcf-9766-3a4e72e42627)


</h1>


 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>
 * [Discord](https://discord.com/invite/0glabs)<br>
 * [Twitter](https://twitter.com/0G_labs)<br>



## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	4|
| RAM	| 8+ GB |
| Storage	| 400 GB SSD |

### 🚧Gerekli kurulumlar
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### 🚧 Go kurulumu
```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### 🚧Dosyaları çekelim
```
git clone https://github.com/initia-labs/initia
cd initia
git checkout v0.2.11
make build
```

```
mkdir -p $HOME/.initia/cosmovisor/genesis/bin
mv /root/initia/build/initiad $HOME/.initia/cosmovisor/genesis/bin/
```
### 🚧System link
```
sudo ln -s $HOME/.initia/cosmovisor/genesis $HOME/.initia/cosmovisor/current -f
sudo ln -s $HOME/.initia/cosmovisor/current/bin/initiad /usr/local/bin/initiad -f
```
### 🚧Cosmovisor indirelim
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```
### 🚧Servis oluşturalım
```
sudo tee /etc/systemd/system/initiad.service > /dev/null << EOF
[Unit]
Description=initia node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.initia"
Environment="DAEMON_NAME=initiad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.initia/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable initiad.service
```
### 🚧Node ayarları
```
initiad config chain-id initiation-1
initiad config keyring-backend os
initiad config node tcp://localhost:15657
```
### 🚧İnit
NOT: node adınızı yazınız.
```
initiad init NODE-ADI-YAZ --chain-id initiation-1
```
### 🚧Genesis addrbook
```
rm ~/.initia/config/genesis.json
curl -Ls https://raw.githubusercontent.com/molla202/pokemon/main/genesis.json > $HOME/.initia/config/genesis.json

```

### 🚧Seed
```
PEERS="093e1b89a498b6a8760ad2188fbda30a05e4f300@35.240.207.217:26656" && \
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.initia/config/config.toml
```
### 🚧Gas pruning ayarı
```
...
```
### 🚧Port Ayarları
```
echo "export N_PORT="15"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```
sed -i.bak -e "s%:1317%:${N_PORT}317%g;
s%:8080%:${N_PORT}080%g;
s%:9090%:${N_PORT}090%g;
s%:9091%:${N_PORT}091%g;
s%:8545%:${N_PORT}545%g;
s%:8546%:${N_PORT}546%g;
s%:6065%:${N_PORT}065%g" $HOME/.initia/config/app.toml
```
```
sed -i.bak -e "s%:26658%:${N_PORT}658%g;
s%:26657%:${N_PORT}657%g;
s%:6060%:${N_PORT}060%g;
s%:26656%:${N_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${N_PORT}656\"%;
s%:26660%:${N_PORT}660%g" $HOME/.initia/config/config.toml
```
### 🚧Snap
```
...
```
### 🚧Başlatalım   
```
sudo systemctl daemon-reload
sudo systemctl restart initiad
```
### 🚧Log
```
sudo journalctl -u initiad.service -f --no-hostname -o cat
```
### 🚧Cüzdan oluşturma
NOT: cüzdan adınızı yazınız
```
initiad keys add cuzdan-adini-yaz
```
- Eski cüzdan import ederkene bele
```
initiad keys add wallet --recover
```

### 🚧Validator oluşturma

NOT: cüzdan adını moniker adınızı yazınız
```
initiad tx staking create-validator \
  --amount=5000000uinit \
  --pubkey=$(initiad tendermint show-validator) \
  --moniker=MONIKER-YAZ \
  --chain-id=initiation-1 \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --min-self-delegation=1 \
  --from=CUZDAN-ADI-YAZ \
  --identity="" \
  --website="" \
  --details="" \
  --node=http://localhost:15657 \
  -y
```
### Kendine delege
NOT: 
```bash
initiad tx staking delegate $(initiad keys show wallet --bech val -a)  miktar000000uinit --from wallet --node=http://localhost:15657 -y
```




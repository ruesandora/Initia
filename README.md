# Initia

> Selamlar Initia Ödüllü Testneti Başladı - Node ve App testneti hakkında yapmanız gerekenleri anlatıyorum adım adım.

> Tüm EXP'leri toplayacağım ve Node'un peşini şimdilik bırakmyacağım

> Node içinde görevler olacak Telegramda paylaşacağım

> Not: Repo'yu hızlıca hazıralayıp paylaşıp 1-2 saat içerssinde sık sık update edicem f5 basarsınız.

> Not2: Repoyu sıfırdan hazırlama mümkünatım yok şu an önemli proje diye bir çok yerden parça parça peer-seed ve snapshot topladım.

## Donanım

```
4 CPU - 8 RAM 160 GB SSD
```

## Kurulum

* `#`ile başlayan notları es geçmeyin lütfen.
```console
sudo apt update -y && sudo apt upgrade -y
sudo apt -qy install curl git jq lz4 build-essential

# Tırnakların arasını doldurun
MONIKER="Validatör İsminizi girin"

sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.10.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)


git clone https://github.com/initia-labs/initia.git
cd initia

# uzun sürer build
make build

mkdir -p $HOME/.initia/cosmovisor/genesis/bin
mv build/initiad $HOME/.initia/cosmovisor/genesis/bin/
rm -rf build


sudo ln -s $HOME/.initia/cosmovisor/genesis $HOME/.initia/cosmovisor/current -f
sudo ln -s $HOME/.initia/cosmovisor/current/bin/initiad /usr/local/bin/initiad -f

# uzun sürer
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

## Servis dosyası oluşumu ve node başlatma

```console

# sudo tee 'den EOF'a kadar birleşik
sudo tee /etc/systemd/system/initia.service > /dev/null << EOF
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

sudo systemctl daemon-reload
sudo systemctl enable initia.service
```

## İnitalize işlemleri

```console

# Hepsini toplu girebilirsiniz

initiad config set client chain-id initiation-1
initiad config set client keyring-backend test
initiad config set client node tcp://localhost:17957

initiad init $MONIKER --chain-id initiation-1

curl -Ls https://snapshots.kjnodes.com/initia-testnet/genesis.json > $HOME/.initia/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/initia-testnet/addrbook.json > $HOME/.initia/config/addrbook.json

sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@initia-testnet.rpc.kjnodes.com:17959\"|" $HOME/.initia/config/config.toml

sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml

sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.initia/config/app.toml

sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:17958\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:17957\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:17960\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:17956\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":17966\"%" $HOME/.initia/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:17917\"%; s%^address = \":8080\"%address = \":17980\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:17990\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:17991\"%; s%:8545%:17945%; s%:8546%:17946%; s%:6065%:17965%" $HOME/.initia/config/app.toml

curl -L https://snapshots.kjnodes.com/initia-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.initia
[[ -f $HOME/.initia/data/upgrade-info.json ]] && cp $HOME/.initia/data/upgrade-info.json $HOME/.initia/cosmovisor/genesis/upgrade-info.json

sudo systemctl start initia.service && sudo journalctl -u initia.service -f --no-hostname -o cat
```

## Cüzdan oluşturma ve app

```console
initiad keys add wallet

# buradan gelen cüzdan bilgilerini keplr'a import edin hızlıca.
# Faucetten token alın aşağıda link.
```

> https://faucet.testnet.initia.xyz/


## App'e giriş

> https://app.testnet.initia.xyz/xp

> Cüzdanı import ettikten sonra acil 6 görevi yapıp NFT'leri mintliyorsunuz

> Mintledikten sonra NFT'yi birleştiriyorsunuz.

> Görevler hata verirse tekrar tekrar deneyin çözülüyor.

> NFT birleştikten sonra beslemeyi unutmayın (sayaç var, erkenci olmak bir kaç adım öne atar)

> Sağ alttan Ear More EXP'yi hemen alın (sayaç var)

> 50 EXP için kullanabilirsiniz: `UNOVN51J`

> Jennie'ye sahip çıkalım.

> [Mint](https://init-ai.testnet.initia.xyz/mint/0xf7b2c7393a82d06f87908dd8dd58378f3fef10e83bcfdf7c5fc22c1a185d5097) yapabilirsiniz bunu da.

## Validatör kurulumu:
```console
initiad config set client keyring-backend test

# Aşağıda 'tırnak içlerine' yorumları ekledim okuyup düzenleyin.
initiad tx mstaking create-validator \
--amount 1000000uinit \
--pubkey $(initiad tendermint show-validator) \
--moniker "Validatör İsmi" \
--identity "Yoksa bu satırı sil" \
--details "Rues Community" \
--website "Twitter veya Github Koyun" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas 1000000 \
--gas-prices 0.15uinit \
--node tcp://localhost:17957
```

> [Explorer Adresi ve Örnek](https://scan.testnet.initia.xyz/initiation-1/validators/initvaloper1pukhdez2qnmrprrmnxr7e9mln4ll2upxs96hy7)

> tx hash aldıktan sonra explorerda aratın biraz aşağıda validatör adresiniz olcak oraya tıkalyın

<img width="1287" alt="Ekran Resmi 2024-05-15 00 51 09" src="https://github.com/ruesandora/Initia/assets/101149671/1c3c114a-9caf-4560-b963-59aad7804bf6">

> Hayırlı olsun, repoyu zamanla güncelleyeceğim ve node görevlerini ekleyeceğim bu repo'ya dosya olarak.

## Görevler

> Görev-1: [Oracle](https://github.com/ruesandora/Initia/blob/main/%231-Oracle.md)

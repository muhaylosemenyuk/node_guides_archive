# Quasar

## Нода
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

#### Підготовка сервера
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```
#### Ставимо Go (одна команда)
```bash
ver="1.20.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
> go version go1.20.1 linux/amd64
#### Задаємо перемінні
##### Придумуємо назву ноди та міняємо замість <your_moniker>
```bash
QUASAR_MONIKER=<your_moniker>
```
#### Зберігаємо перемінні в bash
```bash
echo 'export QUASAR_MONIKER=='$QUASAR_MONIKER >> $HOME/.bash_profile
echo "export QUASAR_CHAIN_ID=qsr-questnet-04" >> $HOME/.bash_profile
echo "export QUASAR_PORT=17" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Качаємо бінарні файли
```bash
cd $HOME
wget https://github.com/quasar-finance/binary-release/raw/main/v0.0.2-alpha-11/quasarnoded-linux-amd64
chmod +x quasarnoded-linux-amd64
if [ ! -d "$HOME/go/bin" ]; then
mkdir $HOME/go/bin
fi
sudo mv quasarnoded-linux-amd64 $HOME/go/bin/quasarnoded
```
#### Перевіряємо версію
```bash
andromedad version
```
> 0.0.2-alpha-11

#### Ініціалізовуємо ноду
    quasarnoded init $QUASAR_MONIKER --chain-id $QUASAR_CHAIN_ID
#### Качаємо генезис
```bash
wget -O $HOME/.quasarnode/config/genesis.json https://files.itrocket.net/testnet/quasar/genesis.json
sha256sum $HOME/.quasarnode/config/genesis.json
```
> 367fb6b6df109841660765701f0996d2a8154438155b45228da05662e5ea9c42

#### Налаштовуємо порти
```bash
#config.toml 
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${QUASAR_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${QUASAR_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${QUASAR_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${QUASAR_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${QUASAR_PORT}660\"%" $HOME/.quasarnode/config/config.toml

# app.toml 
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${QUASAR_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${QUASAR_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${QUASAR_PORT}7\"%" $HOME/.quasarnode/config/app.toml

# client.toml 
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${QUASAR_PORT}657\"%" $HOME/.quasarnode/config/client.toml

external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${QUASAR_PORT}656\"/" $HOME/.quasarnode/config/config.toml
```
#### Налаштовуємо конфіг
```bash
quasarnoded config chain-id $QUASAR_CHAIN_ID
quasarnoded config keyring-backend test

# налаштовуємо мінімальну ціну за газ
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0.0uqsr"/g' $HOME/.quasarnode/config/app.toml

# Включаємо прометеус
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.quasarnode/config/config.toml

# Виключаємо індексацію
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.quasarnode/config/config.toml

# додаємо сіди/піри
SEEDS=""
PEERS="6ccfdbe91c06698f0a66cf95a249dbcd88b5aaa4@quasar-testnet-peer.itrocket.net:443,bba6e85e3d1f1d9c127324e71a982ddd86af9a99@88.99.3.158:18256,bcb8d2b5d5464cddbab9ce2705aae3ad3e38aeac@144.76.67.53:2490,1c1043ae487c91209fce8c589a5772a7f3846e7c@136.243.88.91:8080,1112acc7479a8a1afb0777b0b9a39fb1f7e77abd@34.175.69.87:26656,bffb10a5619be7bfa98919e08f4a6bef4f8f6bf0@135.181.210.186:26656,695b9dc49a979e4c5986c5ae9a6effc8bc8597f0@185.197.250.151:27656,8937bdacf1f0c8b2d1ffb4606554eaf08bd55df4@5.75.255.107:26656,a23f002bda10cb90fa441a9f2435802b35164441@38.146.3.203:18256,41ee7632f310c035235828ce03c208dbe1e24d7d@38.146.3.204:18256,966acc999443bae0857604a9fce426b5e09a7409@65.108.105.48:18256"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.quasarnode/config/config.toml

# Налаштовуємо прунінг
pruning="nothing"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.quasarnode/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.quasarnode/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.quasarnode/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.quasarnode/config/app.toml

# Включаємо фільтр поганих пірів
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.quasarnode/config/config.toml
```
#### Створюємо сервісний файл
```bash
sudo tee /etc/systemd/system/quasarnoded.service > /dev/null <<EOF
[Unit]
Description=quasar
After=network-online.target

[Service]
User=$USER
ExecStart=$(which quasarnoded) start --home $HOME/.quasarnode
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Запускаємо ноду
```bash
sudo systemctl daemon-reload
sudo systemctl enable quasarnoded
sudo systemctl restart quasarnoded && sudo journalctl -u quasarnoded -f -o cat
```
#### Подивитись логи
```bash
sudo journalctl -u quasarnoded -f -o cat
```
#### Перевірити статус синхронізації
```bash
quasarnoded status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
> "catching_up": false - означає що нода синхронізована
> [Перевірити висоту](https://quasar.explorers.guru/)

## Снапшот
```bash
sudo systemctl stop quasarnoded

cp $HOME/.quasarnode/data/priv_validator_state.json $HOME/.quasarnode/priv_validator_state.json.backup

rm -rf $HOME/.quasarnode/data $HOME/.quasarnode/wasm
curl https://files.itrocket.net/testnet/quasar/snap_quasar.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.quasarnode

mv $HOME/.quasarnode/priv_validator_state.json.backup $HOME/.quasarnode/data/priv_validator_state.json

sudo systemctl restart quasarnoded && sudo journalctl -u quasarnoded -f -o cat
```
## State Sync
```bash
sudo systemctl stop quasarnoded

cp $HOME/.quasarnode/data/priv_validator_state.json $HOME/.quasarnode/priv_validator_state.json.backup
quasarnoded tendermint unsafe-reset-all --home $HOME/.quasarnode

peers="6ccfdbe91c06698f0a66cf95a249dbcd88b5aaa4@quasar-testnet-peer.itrocket.net:17656,a23f002bda10cb90fa441a9f2435802b35164441@38.146.3.203:18256,bffb10a5619be7bfa98919e08f4a6bef4f8f6bf0@135.181.210.186:26656,be6acbd97513a61db028e51c9d380e7df0c3a278@213.202.219.160:29656,dc1fc65cfe57222a6ede3a6093097775cf453e0a@78.46.182.169:29656,1c5dd597b2edd6f16d659c5273de1fbc3c3d0e3f@116.202.227.117:48656,a1008e0ea2568c95d16043cc0feb82c56040049d@81.0.248.60:18256,aea911ba7d0a33f69665ab346e68ff9779933e0a@194.126.172.250:56656,33ca2d5e68401a97eee05185f2ac29af648a018e@5.9.81.251:34656,1b50ae662074372c631d639e887af94f6809448f@45.92.163.194:26656,399a67da58bf875821372610d56f6b94dac08504@222.239.92.54:26656"  
SNAP_RPC="https://quasar-testnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.quasarnode/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.quasarnode/config/config.toml

curl https://files.itrocket.net/testnet/quasar/wasm_quasar.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.quasarnode
mv $HOME/.quasarnode/priv_validator_state.json.backup $HOME/.quasarnode/data/priv_validator_state.json

sudo systemctl restart quasarnoded && sudo journalctl -u quasarnoded -f -o cat
```
## Addrbook
```bash
wget -O $HOME/.quasarnode/config/addrbook.json https://files.itrocket.net/testnet/quasar/addrbook.json
sudo systemctl restart quasarnoded && sudo journalctl -u quasarnoded -f -o cat
```
## Гаманець
#### Створюємо гаманець
```bash
quasarnoded keys add wallet
```
#### Відновити гаманець
```bash
quasarnoded keys add wallet --recover
```
#### Зберігаємо адреси
```bash
QUASAR_ADDRESS=$(quasarnoded keys show wallet -a)
QUASAR_VALOPER=$(quasarnoded keys show wallet --bech val -a)
echo "export QUASAR_ADDRESS="${QUASAR_ADDRESS} >> $HOME/.bash_profile
echo "export QUASAR_VALOPER="${QUASAR_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Кран
Робимо запит в [Discord](https://discord.com/channels/927881718189404220/1072185473373569185)

    !faucet QSR <your_address>
#### Перевірити баланс
```bash
quasarnoded q bank balances $QUASAR_ADDRESS
```

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

## Валідатор
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

#### Створюємо валідатора
Створити ключ identity можна [тут](https://keybase.io/)
```bash
andromedad tx staking create-validator \
--amount=1000000uandr \
--pubkey=$(andromedad tendermint show-validator) \
--moniker=$ANDROMEDA_MONIKER \
--website="Посилання на свій сайт чи сторінку в інтернеті" \
--identity=<identity> \
--details="Кілька слів про себе" \
--chain-id=$ANDROMEDA_CHAIN_ID \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--fees=1000uandr \
--from=wallet \
-y
```
Перевіряємо себе в списку в [експлоуері](https://andromeda.explorers.guru/validators)

#### Записуємо valoper_address в bash
```bash
# Замінюємо <your_valoper_address> на адресу валідатора, починається на andrvaloper....
ANDROMEDA_VALOPER=<your_valoper_address> 
echo "export ANDROMEDA_VALOPER=$ANDROMEDA_VALOPER" >> $HOME/.bash_profile 
source $HOME/.bash_profile
```
#### Редагувати валідатора
```bash
andromedad tx staking edit-validator \
--moniker=$ANDROMEDA_MONIKER \
--website="Посилання на свій сайт чи сторінку в інтернеті" \
--identity=<identity> \
--details="Кілька слів про себе" \
--chain-id=$ANDROMEDA_CHAIN_ID \
--commission-rate=0.1 \
--fees=1000uandr \
--from=wallet \
-y
```

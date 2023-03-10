# Nolus

## Зміст
1. [Про проєкт](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%BF%D1%80%D0%BE-%D0%BF%D1%80%D0%BE%D1%94%D0%BA%D1%82)
2. [Сервер](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80)
3. [Нода](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%BD%D0%BE%D0%B4%D0%B0)
4. [Снапшот](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%81%D0%BD%D0%B0%D0%BF%D1%88%D0%BE%D1%82)
5. [State Sync]() 
5. [Addrbook](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%81%D0%BD%D0%B0%D0%BF%D1%88%D0%BE%D1%82)
6. [Гаманець](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B3%D0%B0%D0%BC%D0%B0%D0%BD%D0%B5%D1%86%D1%8C)
7. [Валідатор](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B2%D0%B0%D0%BB%D1%96%D0%B4%D0%B0%D1%82%D0%BE%D1%80)
8. [Резервна копія](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%80%D0%B5%D0%B7%D0%B5%D1%80%D0%B2%D0%BD%D0%B0-%D0%BA%D0%BE%D0%BF%D1%96%D1%8F)
9. [Корисні команди](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%BA%D0%BE%D1%80%D0%B8%D1%81%D0%BD%D1%96-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%B8)
10. [Видалит ноду](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%80%D0%B5%D0%B7%D0%B5%D1%80%D0%B2%D0%BD%D0%B0-%D0%BA%D0%BE%D0%BF%D1%96%D1%8F)

## Про проєкт
**Andromeda** — це рівень платформи додатків, який об’єднує всі публічні блокчейни в екосистемі Cosmos. Завдяки нашій величезній бібліотеці смарт-контрактів без коду користувачі можуть використовувати потужність web3.

> [Discord](https://discord.gg/6BADYVyd) | 
> [Twitter](https://twitter.com/AndromedaProt) | 
> [Github](https://github.com/andromedaprotocol) | 
> [Explorer](https://andromeda.explorers.guru/validators)


## Сервер
Мінімальні: 4 CPU / 8 RAM / 100 SSD or NVME

Рекомендовані: 8 CPU / 64 RAM / 1ТБ SSD or NVME

[Webtropia](https://www.webtropia.com/?kwk=251026062204230100165096) - Cloud VPS S (4/8/200) - 4.7 євро/міс

[Contabo](https://contabo.com/en/vps/vps-s-ssd/?image=ubuntu.267&qty=1&contract=1&storage-type=vps-s-200-gb-ssd) - CLOUD VPS S (4/8/200) - 7.19 євро/міс + 7.19 за одноразову установку

[Hetzner](https://console.hetzner.cloud/projects) - CX21 (2/4/40) - 5.35 євро/міс ([бонус 20 євро за реєстрацію нового акаунту](https://hetzner.cloud/?ref=Hca8pe6nY1rd))

Також ноду можна підселити на інші сервери із вільними ресурсами

[Hetzner Auction](https://www.hetzner.com/sb) - ціни постійно міняються

[Hetzner AX41-NVMe](https://robot.hetzner.com/order) - 37.3 євро/міс + 39 євро одноразово

[Як орендувати дедік на Hetzner](https://teletype.in/@muhaylo.semenyuk/O0-W-CHHZDm)

## Нода
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

#### підготовка сервера
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
NOLUS_MONIKER=<your_moniker>
```
#### Зберігаємо перемінні в bash
```bash
echo 'export NOLUS_MONIKER='$NOLUS_MONIKER >> $HOME/.bash_profile
echo "export NOLUS_CHAIN_ID=nolus-rila" >> $HOME/.bash_profile
echo "export NOLUS_PORT=16" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Качаємо бінарні файли
```bash
cd $HOME && git clone https://github.com/Nolus-Protocol/nolus-core.git
cd nolus-core
git checkout v0.1.43
make install
```
#### Перевіряємо версію
```bash
nolusd version
```
> 0.1.43

#### Ініціалізовуємо ноду
    nolusd init $NOLUS_MONIKER --chain-id $NOLUS_CHAIN_ID
#### Качаємо генезис
```bash
wget -O $HOME/.nolus/config/genesis.json "https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/genesis.json"
sha256sum $HOME/.nolus/config/genesis.json
```
> d22ea6488afe58478c54afeb2d6b5a45622c797dfd75c91a8653eb1f094173c5

#### Налаштовуємо порти
```bash
#config.toml 
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${NOLUS_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${NOLUS_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${NOLUS_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${NOLUS_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${NOLUS_PORT}660\"%" $HOME/.nolus/config/config.toml

# app.toml 
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${NOLUS_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${NOLUS_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${NOLUS_PORT}7\"%" $HOME/.nolus/config/app.toml

# client.toml 
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${NOLUS_PORT}657\"%" $HOME/.nolus/config/client.toml

external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${NOLUS_PORT}656\"/" $HOME/.nolus/config/config.toml
```
#### Налаштовуємо конфіг
```bash
nolusd config chain-id $NOLUS_CHAIN_ID
nolusd config keyring-backend test

# налаштовуємо мінімальну ціну за газ
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025unls\"/;" ~/.nolus/config/app.toml

# додаємо сіди/піри
SEEDS=""
PEERS="5c236704215735ea722a3ca742a5161c2e871ec6@nolus-testnet.nodejumper.io:29656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.nolus/config/config.toml

# Налаштовуємо прунінг
pruning="nothing"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.nolus/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.nolus/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.nolus/config/app.toml

# Включаємо фільтр поганих пірів
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.nolus/config/config.toml
```
#### Створюємо сервісний файл
```bash
tee /etc/systemd/system/nolusd.service > /dev/null <<EOF
[Unit]
Description=nolusd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which nolusd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
#### Запускаємо ноду
```bash
systemctl daemon-reload
systemctl enable nolusd
systemctl restart nolusd && journalctl -u nolusd -f -o cat
```
#### Подивитись логи
```bash
journalctl -u nolusd -f -o cat
```
#### Перевірити статус синхронізації
```bash
nolusd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
> "catching_up": false - означає що нода синхронізована
> [Перевірити висоту](https://nolus.explorers.guru/)
## Снапшот
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

```bash
cd $HOME
sudo apt update && sudo apt install lz4 -y

sudo systemctl stop nolusd

cp $HOME/.nolus/data/priv_validator_state.json $HOME/.nolus/priv_validator_state.json.backup 

nolusd tendermint unsafe-reset-all --home $HOME/.nolus --keep-addr-book 
curl https://snapshots1-testnet.nodejumper.io/nolus-testnet/nolus-rila_2023-03-10.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nolus

mv $HOME/.nolus/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json 

sudo systemctl start nolusd
sudo journalctl -u nolusd -f --no-hostname -o cat
```
## State Sync
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)
```bash
sudo systemctl stop nolusd

cp $HOME/.nolus/data/priv_validator_state.json $HOME/.nolus/priv_validator_state.json.backup
nolusd tendermint unsafe-reset-all --home $HOME/.nolus --keep-addr-book

SNAP_RPC="https://nolus-testnet.nodejumper.io:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height)
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000))
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

PEERS="5c236704215735ea722a3ca742a5161c2e871ec6@nolus-testnet.nodejumper.io:29656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.nolus/config/config.toml

sed -i 's|^enable *=.*|enable = true|' $HOME/.nolus/config/config.toml
sed -i 's|^rpc_servers *=.*|rpc_servers = "'$SNAP_RPC,$SNAP_RPC'"|' $HOME/.nolus/config/config.toml
sed -i 's|^trust_height *=.*|trust_height = '$BLOCK_HEIGHT'|' $HOME/.nolus/config/config.toml
sed -i 's|^trust_hash *=.*|trust_hash = "'$TRUST_HASH'"|' $HOME/.nolus/config/config.toml

mv $HOME/.nolus/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json

curl -s https://snapshots1-testnet.nodejumper.io/nolus-testnet/wasm.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nolus

sudo systemctl restart nolusd
sudo journalctl -u nolusd -f --no-hostname -o cat
```
## Addrbook
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

```bash
curl -s https://snapshots1-testnet.nodejumper.io/nolus-testnet/addrbook.json > $HOME/.nolus/config/addrbook.json
sudo systemctl restart nolusd && sudo journalctl -u nolusd -f -o cat
```

## Гаманець
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

#### Створюємо гаманець
```bash
nolusd keys add wallet
```
#### Відновити гаманець
```bash
nolusd keys add wallet --recover
```
#### Зберігаємо адреси
```bash
NOLUS_ADDRESS=$(nolusd keys show wallet -a)
NOLUS_VALOPER=$(nolusd keys show wallet --bech val -a)
echo "export NOLUS_ADDRESS="${NOLUS_ADDRESS} >> $HOME/.bash_profile
echo "export NOLUS_VALOPER="${NOLUS_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Кран
Робимо запит в [Discord](https://discord.com/channels/932744941162098688/1019632226113175552)

    !request <your_address>
#### Перевірити баланс
```bash
nolusd q bank balances $NOLUS_ADDRESS
```

## Валідатор
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

#### Створюємо валідатора
Створити ключ identity можна [тут](https://keybase.io/)
```bash
nolusd tx staking create-validator \
--amount=1000000unls \
--pubkey=$(andromedad tendermint show-validator) \
--moniker=$NOLUS_MONIKER \
--website="Посилання на свій сайт чи сторінку в інтернеті" \
--identity=<identity> \
--details="Кілька слів про себе" \
--chain-id=$NOLUS_CHAIN_ID \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--fees=1000unls \
--from=wallet \
-y
```
Перевіряємо себе в списку в [експлоуері](https://nolus.explorers.guru/)

#### Редагувати валідатора
```bash
nolusd tx staking edit-validator \
--moniker=$NOLUS_MONIKER \
--website="Посилання на свій сайт чи сторінку в інтернеті" \
--identity=<identity> \
--details="Кілька слів про себе" \
--chain-id=$NOLUS_CHAIN_ID \
--commission-rate=0.1 \
--fees=1000unls \
--from=wallet \
-y
```

## Резервна копія
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

Зберігаємо priv_validator_key.json що знаходиться за адресою /root/.nolus/config/

Перетягуємо мишкою файл з моби

![image](https://user-images.githubusercontent.com/79005788/219518885-78ab7648-c750-428c-b7c1-1d7375a1d777.png)

**Або** (для власників макбука)
Для копіювання файлу відкриваємо нове вікно терміналу на нашому комп'ютері
> Замість 0.0.0.0 вводимо ip свого сервера
> Замість <Шлях до папки>, шлях до вашої папки куди зберегти, наприклад G:\Ноди\andromeda

```bash
scp root@0.0.0.0:/root/.nolus/config/priv_validator_key.json <Шлях до папки>
```
## Корисні команди
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

##### Перезапустити ноду
```bash
systemctl restart nolusd
```
##### Подивитись логи
```bash
journalctl -u nolusd -f -o cat

# Вихід з логів через ctrl+c
```
##### Перевірити статус синхронізації
```bash
nolusd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
##### Перевіряємо баланс
```bash
nolusd q bank balances $NOLUS_ADDRESS
```
##### Перевірити список гаманців
```bash
nolusd keys list
```
##### Зібрати реварди з усіх валідаторів, кому делегували (без комісій)
```bash
nolusd tx distribution withdraw-all-rewards --from wallet -y
```
##### Зібрати реварди з окремого валідатора або реварди + комісію зі свого валідатора
```bash
nolusd tx distribution withdraw-rewards $NOLUS_VALOPER --from wallet --commission -y
```
##### Заделегувати у свого валідатора (1nls = 1000000unls)
```bash
nolusd tx staking delegate $NOLUS_VALOPER 1000000unls --from wallet -y
```
##### Переделегувати на іншого валідатора
```bash
# <src-validator-addr> - адреса валідатора з якого зняти делегацію
# <dst-validator-addr> - адреса валідатора кому передати делегацію
nolusd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000unls --from wallet -y
```
###### забрати делегацію 
```bash
nolusd tx staking unbond $NOLUS_VALOPER 1000000unls --from wallet -y
```
##### Відправити монети на іншу адресу
```bash
# <address> - куди відправити
nolusd tx bank send wallet <address> 1000000unls -y
```
##### Вийти з тюрми
```bash
nolusd tx slashing unjail --from wallet -y
```
##### Проголосувати (замість 1 вказуємо номер пропозалу)
```bash
nolusd tx gov vote 1 yes --from wallet -y
```
##### Перевірити чи проголосували
```bash
nolusd q gov vote 1 $NOLUS_ADDRESS
```
## Видалити ноду
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

```bash
systemctl stop nolusd && \
systemctl disable nolusd && \
rm /etc/systemd/system/nolusd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .nolus nolus && \
rm -rf $(which nolusd)
```

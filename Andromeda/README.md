# Andromeda | Galileo-3
![image](https://user-images.githubusercontent.com/79005788/219491087-1beda6cb-c4c7-4524-b517-f85a3a843179.png)

## Зміст
1. [Про проєкт](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%BF%D1%80%D0%BE-%D0%BF%D1%80%D0%BE%D1%94%D0%BA%D1%82)
2. [Сервер](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80)
3. [Нода](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%BD%D0%BE%D0%B4%D0%B0)
4. [Снапшот](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D1%81%D0%BD%D0%B0%D0%BF%D1%88%D0%BE%D1%82)
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
ANDROMEDA_MONIKER=<your_moniker>
```
#### Зберігаємо перемінні в bash
```bash
echo 'export ANDROMEDA_MONIKER='$ANDROMEDA_MONIKER >> $HOME/.bash_profile
echo "export ANDROMEDA_CHAIN_ID=galileo-3" >> $HOME/.bash_profile
echo "export ANDROMEDA_PORT=15" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Качаємо бінарні файли
```bash
cd $HOME && git clone https://github.com/andromedaprotocol/andromedad.git
cd andromedad
git checkout galileo-3-v1.1.0-beta1
make install
```
#### Перевіряємо версію
```bash
andromedad version
```
> galileo-3-v1.1.0-beta1

#### Ініціалізовуємо ноду
    andromedad init $ANDROMEDA_MONIKER --chain-id $ANDROMEDA_CHAIN_ID
#### Качаємо генезис
```bash
wget -O $HOME/.andromedad/config/genesis.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/AndromedaProtocol/genesis.json"
sha256sum $HOME/.andromedad/config/genesis.json
```
> 26cefef408b9cdc013e7427d5fb05bbd44a006d80b7e0db36aaf125edda9b4e6

#### Налаштовуємо порти
```bash
#config.toml 
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${ANDROMEDA_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${ANDROMEDA_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${ANDROMEDA_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${ANDROMEDA_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${ANDROMEDA_PORT}660\"%" $HOME/.andromedad/config/config.toml

# app.toml 
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${ANDROMEDA_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${ANDROMEDA_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${ANDROMEDA_PORT}7\"%" $HOME/.andromedad/config/app.toml

# client.toml 
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${ANDROMEDA_PORT}657\"%" $HOME/.andromedad/config/client.toml

external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${ANDROMEDA_PORT}656\"/" $HOME/.andromedad/config/config.toml
```
#### Налаштовуємо конфіг
```bash
andromedad config chain-id $ANDROMEDA_CHAIN_ID
andromedad config keyring-backend test

# налаштовуємо мінімальну ціну за газ
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0uandr\"/;" ~/.andromedad/config/app.toml

# додаємо сіди/піри
SEEDS=""
PEERS="9d058b4c4eb63122dfab2278d8be1bf6bf07f9ef@andromeda-testnet.nodejumper.io:26656,9d058b4c4eb63122dfab2278d8be1bf6bf07f9ef@andromeda-testnet.nodejumper.io:26656,e4d0c8cf6a3dbee8af43582bb14e6e1077028341@198.244.179.125:30170,f17030edb4e4ec7143c3e3bbbfaeee3dd1a619f2@194.34.232.224:56656,5e5186020063f7f8a3f3c6c23feca32830a18f33@65.109.174.30:56656,6006190d5a3a9686bbcce26abc79c7f3f868f43a@37.252.184.230:26656,aeccd559d0d6f8dfb4efcf311fbad3e80ae0e87f@217.160.26.225:26656,67b79549c8890782e3cf44e3fe4688e15ff37929@65.109.19.93:27412,50ca8e25cf1c5a83aa4c79bb1eabfe88b20eb367@65.108.199.120:61356,06d4ab2369406136c00a839efc30ea5df9acaf11@10.128.0.44:26656,43d667323445c8f4d450d5d5352f499fa04839a8@192.168.0.237:26656,29a9c5bfb54343d25c89d7119fade8b18201c503@192.168.101.79:26656,6006190d5a3a9686bbcce26abc79c7f3f868f43a@37.252.184.230:26656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.andromedad/config/config.toml

# Налаштовуємо прунінг
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.andromedad/config/app.toml

# Включаємо фільтр поганих пірів
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.andromedad/config/config.toml
```
#### Створюємо сервісний файл
```bash
sudo tee /etc/systemd/system/andromedad.service > /dev/null <<EOF
[Unit]
Description=andromedad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which andromedad) start
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
sudo systemctl enable andromedad
sudo systemctl restart andromedad && sudo journalctl -u andromedad -f -o cat
```
#### Подивитись логи
```bash
sudo journalctl -u andromedad -f -o cat
```
#### Перевірити статус синхронізації
```bash
andromedad status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
> "catching_up": false - означає що нода синхронізована
> [Перевірити висоту](https://andromeda.explorers.guru/)
## Снапшот
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

```bash
cd $HOME
apt install lz4
cp $HOME/.andromedad/data/priv_validator_state.json $HOME/.andromedad/priv_validator_state.json.backup
rm -rf $HOME/.andromedad/data
curl -o - -L http://andromedad.snapshot.stavr.tech:1021/andromedad/andromedad-snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.andromedad --strip-components 2
curl -o - -L http://andromedad.wasm.stavr.tech:1002/wasm-andromedad.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.andromedad --strip-components 2
mv $HOME/.andromedad/priv_validator_state.json.backup $HOME/.andromedad/data/priv_validator_state.json
```
## Addrbook
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

```bash
wget -O $HOME/.andromedad/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/AndromedaProtocol/addrbook.json"
systemctl restart andromedad && journalctl -u andromedad -f -o cat
```
## Гаманець
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

#### Створюємо гаманець
```bash
andromedad keys add wallet
```
#### Відновити гаманець
```bash
andromedad keys add wallet --recover
```
#### Зберігаємо адресу
###### Замініть <your_address> на адресу вашого гаманця
```bash
ANDROMEDA_ADDRESS=<your_address>
echo "export ANDROMEDA_ADDRESS=$ANDROMEDA_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Кран
Робимо запит в [Discord](https://discord.com/channels/932744941162098688/1019632226113175552)

    !request <your_address>
#### Перевірити баланс
```bash
andromedad q bank balances $ANDROMEDA_ADDRESS
```
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

## Резервна копія
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

Зберігаємо priv_validator_key.json що знаходиться за адресою /root/.andromedad/config/

Перетягуємо мишкою файл з моби

![image](https://user-images.githubusercontent.com/79005788/219518885-78ab7648-c750-428c-b7c1-1d7375a1d777.png)

**Або** (для власників макбука)
Для копіювання файлу відкриваємо нове вікно терміналу на нашому комп'ютері
> Замість 0.0.0.0 вводимо ip свого сервера
> Замість <Шлях до папки>, шлях до вашої папки куди зберегти, наприклад G:\Ноди\andromeda

```bash
scp root@0.0.0.0:/root/.andromedad/config/priv_validator_key.json <Шлях до папки>
```
## Корисні команди
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

##### Перезапустити ноду
```bash
systemctl restart andromedad
```
##### Подивитись логи
```bash
journalctl -u andromedad -f -o cat

# Вихід з логів через ctrl+c
```
##### Перевірити статус синхронізації
```bash
andromedad status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
##### Перевіряємо баланс
```bash
andromedad q bank balances $ANDROMEDA_ADDRESS
```
##### Перевірити список гаманців
```bash
andromedad keys list
```
##### Зібрати реварди з усіх валідаторів, кому делегували (без комісій)
```bash
andromedad tx distribution withdraw-all-rewards --from wallet --fees 1000uandr --gas=300000 -y
```
##### Зібрати реварди з окремого валідатора або реварди + комісію зі свого валідатора
```bash
andromedad tx distribution withdraw-rewards $ANDROMEDA_VALOPER --from wallet --fees 1000uandr --gas=300000 --commission -y
```
##### Заделегувати у свого валідатора (1andr = 1000000uandr)
```bash
andromedad tx staking delegate $ANDROMEDA_VALOPER 1000000uandr --from wallet --fees 1000uandr --gas=300000 -y
```
##### Переделегувати на іншого валідатора
```bash
# <src-validator-addr> - адреса валідатора з якого зняти делегацію
# <dst-validator-addr> - адреса валідатора кому передати делегацію
andromedad tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000uandr --from wallet --fees 1000uandr --gas=300000 -y
```
###### забрати делегацію 
```bash
andromedad tx staking unbond $ANDROMEDA_VALOPER 1000000uandr --from wallet --fees 1000uandr --gas=300000 -y
```
##### Відправити монети на іншу адресу
```bash
# <address> - куди відправити
andromedad tx bank send wallet <address> 1000000uandr --fees 1000uandr --gas=300000 -y
```
##### Вийти з тюрми
```bash
andromedad tx slashing unjail --from wallet --fees 1000uandr --gas=300000 -y
```
##### Проголосувати (замість 1 вказуємо номер пропозалу)
```bash
andromedad tx gov vote 1 yes --from wallet --fees 1000uandr --gas=300000 -y
```
##### Перевірити чи проголосували
```bash
andromedad q gov vote 1 $ANDROMEDA_ADDRESS
```
## Видалити ноду
[Зміст](https://github.com/muhaylosemenyuk/node_guides/tree/main/Andromeda#%D0%B7%D0%BC%D1%96%D1%81%D1%82)

```bash
systemctl stop andromedad && \
systemctl disable andromedad && \
rm /etc/systemd/system/andromedad.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .andromedad andromedad && \
rm -rf $(which andromedad)
```

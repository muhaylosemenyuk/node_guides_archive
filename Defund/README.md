# Defund

## Підготовка сервера
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
DEFUND_MONIKER=<your_moniker>
```
#### Зберігаємо перемінні в bash
```bash
echo 'export DEFUND_MONIKER=='$DEFUND_MONIKER >> $HOME/.bash_profile
echo "export DEFUND_CHAIN_ID=orbit-alpha-1" >> $HOME/.bash_profile
echo "export DEFUND_PORT=13" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Качаємо бінарні файли
```bash
cd $HOME
rm -rf defund
git clone https://github.com/defund-labs/defund.git
cd defund
git checkout v0.2.6
make install
```
#### Перевіряємо версію
```bash
defundd version --long | grep -e version -e commit
```
> version: 0.2.6
> commit: 8f2ebe3d30efe84e013ec5fcdf21a3b99e786c3d
## Ставимо Cosmovisor
```bash
# Качаємо Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0

# Качаємо бінарні файли для Cosmovisor
mkdir -p $HOME/.defund/cosmovisor/genesis/bin
mv build/defundd $HOME/.defund/cosmovisor/genesis/bin/
rm -rf build

ln -s $HOME/.defund/cosmovisor/genesis $HOME/.defund/cosmovisor/current
sudo ln -s $HOME/.defund/cosmovisor/current/bin/defundd /usr/local/bin/defundd

# Створюємо сервісний файл
sudo tee /etc/systemd/system/defundd.service > /dev/null << EOF
[Unit]
Description=defund-testnet node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.defund"
Environment="DAEMON_NAME=defundd"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.defund/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable defundd
```
#### Ініціалізовуємо ноду
    defundd init $DEFUND_MONIKER --chain-id $DEFUND_CHAIN_ID
#### Качаємо генезис
```bash
wget -O $HOME/.defund/config/genesis.json "https://raw.githubusercontent.com/defund-labs/testnet/main/orbit-alpha-1/genesis.json"
sha256sum $HOME/.defund/config/genesis.json
```
> 58916f9c7c4c4b381f55b6274bce9b8b8d482bfb15362099814ff7d0c1496658

#### Налаштовуємо порти
```bash
# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${DEFUND_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${DEFUND_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${DEFUND_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${DEFUND_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${DEFUND_PORT}660\"%" $HOME/.defund/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${DEFUND_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${DEFUND_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${DEFUND_PORT}7\"%" $HOME/.defund/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${DEFUND_PORT}657\"%" $HOME/.defund/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${DEFUND_PORT}656\"/" $HOME/.defund/config/config.toml
```
#### Налаштовуємо конфіг
```bash
defundd config chain-id $DEFUND_CHAIN_ID
defundd config keyring-backend test
defundd config node tcp://localhost:${DEFUND_PORT}657

# налаштовуємо мінімальну ціну за газ
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0ufetf\"|" $HOME/.defund/config/app.toml

# Додаємо сіди
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@defund-testnet.rpc.kjnodes.com:40659\"|" $HOME/.defund/config/config.toml

# Налаштовуємо прунінг
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.defund/config/app.toml
```
#### Снапшот
```bash
curl -L https://snapshots.kjnodes.com/defund-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.defund
[[ -f $HOME/.defund/data/upgrade-info.json ]] && cp $HOME/.defund/data/upgrade-info.json $HOME/.defund/cosmovisor/genesis/upgrade-info.json
```
#### Запускаємо ноду
```bash
systemctl daemon-reload
systemctl enable defundd
systemctl restart defundd && journalctl -u defundd -f -o cat
```
#### Подивитись логи
```bash
sudo journalctl -u defundd -f -o cat
```
#### Перевірити статус синхронізації
```bash
defundd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
> "catching_up": false - означає що нода синхронізована
> [Перевірити висоту](https://defund.explorers.guru/)

## Addrbook
```bash
curl -Ls https://snapshots.kjnodes.com/defund-testnet/addrbook.json > $HOME/.defund/config/addrbook.json
sudo systemctl restart defundd && sudo journalctl -u defundd -f -o cat
```
## Гаманець
#### Створюємо гаманець
```bash
defundd keys add wallet
```
#### Відновити гаманець
```bash
defundd keys add wallet --recover
```
#### Зберігаємо адреси
```bash
DEFAND_ADDRESS=$(defundd keys show wallet -a)
DEFAND_VALOPER=$(defundd keys show wallet --bech val -a)
echo "export DEFAND_ADDRESS="${DEFAND_ADDRESS} >> $HOME/.bash_profile
echo "export DEFAND_VALOPER="${DEFAND_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Кран
Робимо запит в [Discord](https://discord.com/channels/913091321114296330/1038133368841310280)

    !faucet <your_address>
#### Перевірити баланс
```bash
defundd q bank balances $DEFAND_ADDRESS
```
## Валідатор
#### Створюємо валідатора
Створити ключ identity можна [тут](https://keybase.io/)
```bash
defundd tx staking create-validator \
--amount 1000000ufetf \
--pubkey $(defundd tendermint show-validator) \
--moniker $DEFUND_MONIKER \
--identity <identity> \
--details "Кілька слів про себе" \
--website "Посилання на свій сайт чи сторінку в інтернеті" \
--chain-id $DEFUND_CHAIN_ID \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0ufetf \
-y
```
Перевіряємо себе в списку в [експлоуері](https://defund.explorers.guru/validators)
## Резервна копія

Зберігаємо priv_validator_key.json що знаходиться за адресою /root/.defund/config/

Перетягуємо мишкою файл з моби

**Або** (для власників макбука)
Для копіювання файлу відкриваємо нове вікно терміналу на нашому комп'ютері
> Замість 0.0.0.0 вводимо ip свого сервера
> Замість <Шлях до папки>, шлях до вашої папки куди зберегти, наприклад G:\Ноди\defand

```bash
scp root@0.0.0.0:/root/.defund/config/priv_validator_key.json <Шлях до папки>
```
## Корисні команди

##### Перезапустити ноду
```bash
systemctl restart defundd
```
##### Подивитись логи
```bash
journalctl -u defundd -f -o cat

# Вихід з логів через ctrl+c
```
##### Перевірити статус синхронізації
```bash
defundd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
```
##### Перевіряємо баланс
```bash
defundd q bank balances $DEFAND_ADDRESS
```
##### Перевірити список гаманців
```bash
defundd keys list
```
##### Зібрати реварди з усіх валідаторів, кому делегували (без комісій)
```bash
defundd tx distribution withdraw-all-rewards --from wallet --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Зібрати реварди з окремого валідатора або реварди + комісію зі свого валідатора
```bash
defundd tx distribution withdraw-rewards $DEFAND_VALOPER --from wallet --commission --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Заделегувати у свого валідатора (1fetf = 1000000ufetf)
```bash
defundd tx staking delegate $DEFAND_VALOPER 1000000ufetf --from wallet --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Переделегувати на іншого валідатора
```bash
# <src-validator-addr> - адреса валідатора з якого зняти делегацію
# <dst-validator-addr> - адреса валідатора кому передати делегацію
defundd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000ufetf --from wallet --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
###### забрати делегацію 
```bash
defundd tx staking unbond $DEFAND_VALOPER 1000000ufetf --from wallet --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Відправити монети на іншу адресу
```bash
# <address> - куди відправити
defundd tx bank send wallet <address> 1000000ufetf --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Вийти з тюрми
```bash
defundd tx slashing unjail --from wallet --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Проголосувати (замість 1 вказуємо номер пропозалу)
```bash
defundd tx gov vote 1 yes --from wallet --chain-id $DEFUND_CHAIN_ID --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
```
##### Перевірити чи проголосували
```bash
defundd q gov vote 1 $DEFUND_ADDRESS
```
## Видалити ноду

```bash
systemctl stop defundd && \
systemctl disable defundd && \
rm /etc/systemd/system/defundd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .defund defund && \
rm -rf $(which defundd)
```

# Kyve | Kaon-1
## Нода
#### Оновлюємо репозиторії
    apt update && apt upgrade -y
#### Качаємо бінарник
    wget https://files.kyve.network/chain/v1.0.0-rc0/kyved_linux_amd64.tar.gz
    tar -xvzf kyved_linux_amd64.tar.gz
    chmod +x kyved
    sudo mv kyved /usr/local/bin/kyved
    rm kyved_linux_amd64.tar.gz
#### Ініціалізовуємо ноду
    kyved init $NAME_VALIDATOR --chain-id kaon-1
#### Качаємо генезис
    wget https://raw.githubusercontent.com/KYVENetwork/networks/main/kaon-1/genesis.json
    mv genesis.json ~/.kyve/config/genesis.json
#### Додаємо піри
    peers="7820d73c4449e0e4328c9fc4437b00aef8de33c2@5.161.195.113:26656"
    sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.kyve/config/config.toml
#### Включаємо keyring-backend test
    kyved config keyring-backend test
#### Включаємо прунінг
    pruning="custom" && \
    pruning_keep_recent="100" && \
    pruning_keep_every="0" && \
    pruning_interval="10" && \
    sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.kyve/config/app.toml && \
    sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.kyve/config/app.toml && \
    sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.kyve/config/app.toml && \
    sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.kyve/config/app.toml
#### Створюємо сервісний файл
    sudo tee /etc/systemd/system/kyved.service > /dev/null <<EOF
    [Unit] 
    Description=kyve
    After=network-online.target
    [Service] 
    User=$USER
    ExecStart=$(which kyved) start --home $HOME/.kyve
    Restart=on-failure 
    RestartSec=10 
    LimitNOFILE=65535
    [Install] 
    WantedBy=multi-user.target
    EOF
#### Запускаємо ноду
    sudo systemctl restart systemd-journald
    sudo systemctl daemon-reload
    sudo systemctl enable kyved
    sudo systemctl restart kyved
#### Перевірити логи
    sudo journalctl -u kyved -f -o cat
## Гаманець
#### Створюємо гаманець
```bash
kyved keys add wallet
```
#### Відновити гаманець
```bash
kyved keys add wallet --recover
```
#### Зберігаємо адресу
> Замініть <your_address> на адресу вашого гаманця
```bash
KYVE_ADDRESS=<your_address>
echo "export KYVE_ADDRESS=$KYVE_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
#### Перевірити баланс
```bash
kyved q bank balances $KYVE_ADDRESS
```

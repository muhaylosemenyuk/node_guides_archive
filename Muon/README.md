# Muon
## Сервер
4 GB  RAM | CPU 2 Core | 20GB SSD
#### Ставимо докер
```bash
wget https://raw.githubusercontent.com/ProNodes11/NodeGuides/main/docker.sh && chmod +x docker.sh && ./docker.sh
mkdir muon-node && cd muon-node
curl -o docker-compose.yml https://raw.githubusercontent.com/muon-protocol/muon-node-js/testnet/docker-compose-pull.yml
```
#### Запускаємо ноду
```bash
docker compose up -d
```
#### Отримуємо посилання на дашборд
    echo http://$(wget -qO- eth0.me):8000/status
* Копіюємо посилання з терміналу та вставляємо у браузер (залишаємо вкладку відкритою)
* Переходимо за [посиланням](https://alice.muon.net/) та конектимо свій гаманець метамаск, перед цим змінюємо мережу. З'явиться віконце в якому буде посилання на кран TEST BNB де потрібно буде запросити токен
* Мінтимо 1000 ALICE
* NEXT STEP
* APPROVE
* Додаємо Node Address та Peer Id із першої вкладки (посилання на яку брали в терміналі)
![image](https://user-images.githubusercontent.com/79005788/224305512-07335bcd-9a10-42c2-af29-3ad4a8653e51.png)
![image](https://user-images.githubusercontent.com/79005788/224305670-1c0d45db-ca6b-4b83-8b14-ec0bb549f427.png)


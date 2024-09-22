# Отчёт по лабораторной работе DevOps2

## Техническое задание
1. Написать “плохой” Dockerfile, в котором есть не менее трех “bad practices” по написанию докерфайлов;
2. Написать “хороший” Dockerfile, в котором эти плохие практики исправлены;
3. В Readme описать каждую из плохих практик в плохом докерфайле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат;
4. В Readme описать 2 плохих практики по работе с контейнерами. ! Не по написанию докерфайлов, а о том, как даже используя хороший докерфайл можно накосячить именно в работе с контейнерами.

## Установка Docker

Обновляем пакеты
```bash
sudo apt update
```

Устанавливаем дополнительные пакеты
```bash
sudo apt install curl software-properties-common ca-certificates apt-transport-https -y
```
curl — необходим для работы с веб-ресурсами;
software-properties-common — пакет для управления ПО с помощью скриптов;
ca-certificates — содержит информацию о центрах сертификации;
apt-transport-https — необходим для передачи данных по протоколу HTTPS.
Флаг -y означает, что на все вопросы терминала ответом будет «Да».

Импортируем GPG-ключ
```bash
wget -O- https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor | sudo tee /etc/apt/keyrings/docker.gpg > /dev/null
```

Добавляем репозиторий докера
```bash
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable"| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Еще раз обновляем пакеты
```bash
sudo apt update
```

Проверяем репозиторий
```bash
apt-cache policy docker-ce
```

Результат проверки:
![проверка правильного репо](ссылка)

Устанавливаем Docker
```bash
sudo apt install docker-ce -y
```

Проверяем статус докера
```bash
sudo systemctl status docker
```

Результат проверки:
![проверка статуса докера](ссылка)



## Написание "плохого" Dockerfile


## Написание "хорошего" Dockerfile


## "Bad practices" по работе с контейнерами

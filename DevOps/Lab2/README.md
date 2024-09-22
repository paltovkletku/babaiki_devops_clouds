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

Устанавливаем Docker
```bash
sudo apt install docker-ce -y
```

Проверяем статус докера
```bash
sudo systemctl status docker
```

Результат проверки:
![проверка статуса докера](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2/images/docker%20running.jpg)

## Написание "плохого" Dockerfile

Напишем докерфайл с ошибками, а затем разберем их.
```bash
FROM ubuntu:latest

RUN apt-get update

RUN apt-get install bash

RUN apt-get install -y vim

RUN apt-get install -y curl

ADD https://raw.githubusercontent.com/paltovkletku/babaiki_devops_clouds/main/DevOps/Lab2/happy.bash .

CMD ["bash", "happy.bash"]
```

![плохой докерфайл](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2/images/bad.jpg)

Bad practices:

1. Использование ubuntu:latest в качестве базового образа.
   Такая пркатика может вызвать проблемы с контейнером и непредсказуемое поведение. Так как образ будет всегда использовать последнюю версию Ubuntu, отличная от изначальной версии может привести к проблемам совместимости или к уязвимостям безопасности.
   
2. Множественные инструкции RUN.
   Все выполняющиеся инструкции при сборке docker-образа влекут за собой увеличение его размера. В нашем случае создается 4 слоя в образе. Такой подход может повлечь за собой проблемы при загрузке и выгрузке образа, а также замедлить процесс сборки.

3. Установка избыточных пакетов.
   Установка пакетов, которые не используются в приложении, увеличивает размер образа и понижает безопасность.

4. Добавление файла через url-ссылку, использование ADD вместо COPY.
   Не рекомендуется добавлять файлы в образ Docker, ссылаясь на url с помощью ADD. Эта инструкция автоматически извлекает url-адреса, что может привести к уязвимостям безопасности, если url-адрес содержит вредоносный код. Также ADD может скрывать происхождение файлов, что затрудняет отслеживание изменений и поддержание целостности файлов. Стоит отметить, что при использовании ADD загружается весь файл, которой включает в себя ненужные метаданные и зависимости, что приводит к увеличени. размера образа.

## Написание "хорошего" Dockerfile

```bash
FROM ubuntu:22.04

WORKDIR /lab2

COPY happy.bash .

ENTRYPOINT ["bash"]

CMD ["happy.bash"]
```

![хороший докерфайл](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2/images/good.jpg)

## "Bad practices" по работе с контейнерами

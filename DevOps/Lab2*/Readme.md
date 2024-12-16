# Отчёт по лабораторной работе DevOps2*

## Техническое задание

1. Написать “плохой” Docker compose файл, в котором есть не менее трех “bad practices” по их написанию
2. Написать “хороший” Docker compose файл, в котором эти плохие практики исправлены
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат
4. После предыдущих пунктов в хорошем файле настроить сервисы так, чтобы контейнеры в рамках этого compose-проекта так же поднимались вместе, но не "видели" друг друга по сети. В отчете описать, как этого добились и кратко объяснить принцип такой изоляции

## Выполнение работы


Устнавливаем compose

![установка](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D0%BA%D0%BE%D0%BC%D0%BF%D0%BE%D0%B7%D0%B0.jpg)

Сначала сделаем, а потом подумаем, что натворили (пишем плохой docker compose)

```
services:
  web:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
      - NGINX_PORT=80

  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

  cache:
    image: redis
    ports:
      - "6379:6379"

volumes:
  db_data:
```

А теперь запускаем:

![запуск плохого1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%B7%D0%B0%D0%BF%D1%83%D1%81%D1%82%D0%B8%D0%BB%D0%B8%20%D0%BF%D0%BB%D0%BE%D1%85%D0%BE%D0%B9%20-%20%D0%BB%D0%BE%D0%B3%D0%B81.jpg)

![запуск плохого2](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%BB%D0%BE%D0%B3%D0%B82.jpg)

![запуск плохого3](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%BB%D0%BE%D0%B3%D0%B83.jpg)

Посмотрели, останавливаем это чудище:

![остановка плохого](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%BE%D1%82%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D0%B5%20%D0%BF%D0%BB%D0%BE%D1%85%D0%BE%D0%B3%D0%BE.jpg) 

Самое время подумать, что было не так...

1. Кажется, кто-то забыл/не подумал, что могут быть разные версии nginx и mysql... так не делаем, нужно указать конкретную, чтобы избежать неприятных сюрпризов в поведении приложений при обновлении образов.
  
   Запомним: надо указывать версии в хорошем docker compose.

![а-ой](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%B1%D0%B5%D0%B7%D0%BE%D0%BF%D0%B0%D1%81%D0%BD%D0%BE%D1%81%D1%82%D1%8C.jpg)

о чем это мы?.. да, у нас проблемы с безопасностью...

2. Наши сервисы имеют открытые порты. Получаем минус безопасность, а еще возможность возникновения конфликтов портов.
   Чтобы такое решить, изменим порт nginx на 8080, чтобы избежать конфликта с другими сервисами

3. Так-с, а изолировать сети кто будет?.. Да, этого мы пока не сделали, чем это нам грозит? Наши сервисы находятся в одной сети по умолчанию, что может привести к нежелательному взаимодействию между ними.
  
   Так не делаем, делаем по-другому: создаем отдельные сети (db_network и cache_network) для изоляции сервисов. Контейнеры в одной сети не смогут "видеть" или взаимодействовать с контейнерами в другой сети (они изолированы друг от друга). Так мы решим проблему с конфликтами и нежелательными взаимодействиями.

Давайте добавим еще красивую деталь

![секрет](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82.png)

4. Для хранения конфиденциальной информации будем использовать docker secrets

Создаем директорию

![дир](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82%D1%8B-%D0%BD%D0%B0%D1%87%D0%B0%D0%BB%D0%BE.jpg)

В код добавим такой кусочек:

```
secrets:
  mysql_root_password:
    file: ./secrets/mysql_root_password.txt
  mysql_user_password:
    file: ./secrets/mysql_user_password.txt
```

А теперь, оглядываясь на все недочеты и предложения по исправлению, пишем хороший docker compose:

```
services:
  web:
    image: nginx:1.21
    ports:
      - "8080:80"
    environment:
      NGINX_HOST: localhost
      NGINX_PORT: 80

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/mysql_root_password
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD_FILE: /run/secrets/mysql_user_password
    networks:
      - db_network
    secrets:
      - mysql_root_password
      - mysql_user_password

  cache:
    image: redis:alpine
    networks:
      - cache_network

networks:
  db_network:
  cache_network:

secrets:
  mysql_root_password:
    file: ./secrets/mysql_root_password.txt
  mysql_user_password:
    file: ./secrets/mysql_user_password.txt

```

Запускаем:

![запуск плохого1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%85%D0%BE%D1%80%D0%BE%D1%88%D0%B8%D0%B9(1)1.jpg)

![запуск плохого2](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%85%D0%BE%D1%80%D0%BE%D1%88%D0%B8%D0%B9(1)2.jpg)

![запуск плохого3](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%85%D0%BE%D1%80%D0%BE%D1%88%D0%B8%D0%B9(1)3.jpg)

Спасибо, останавливаемся

![запуск плохого](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%BE%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D1%85%D0%BE%D1%80%D0%BE%D1%88%D0%B5%D0%B3%D0%BE(1).jpg)



Сейчас давайте вернемся к вопросу об изоляции

У нас была проблема: сервисы находились в одной сети (по умолчанию). Так сервисы могут взаимодействовать друг с другом, а нам бы такого не хотелось

В хорошем файле мы создали отдельные сети (db_network и cache_network) для изоляции. 
Сервисы db и cache были помещены в соответсвующие сети. Это означает, что контейнеры в одной сети не могут "видеть" или взаимодействовать с контейнерами в другой сети.

Так как Docker использует сети для управления взаимодействием между контейнерами, контейнеры, находящиеся в одной сети, могут общаться друг с другом по именам сервисов. Если они находятся в разных сетях, они изолированы друг от друга (это повышает безопасность и управляемость приложения).

Таким образом, изоляция сервисов позволяет избежать конфликтов и нежелательных взаимодействий.


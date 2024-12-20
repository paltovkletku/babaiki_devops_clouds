# Отчёт по лабораторной работе DevOps2*

## Техническое задание

1. Написать “плохой” Docker compose файл, в котором есть не менее трех “bad practices” по их написанию
2. Написать “хороший” Docker compose файл, в котором эти плохие практики исправлены
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат
4. После предыдущих пунктов в хорошем файле настроить сервисы так, чтобы контейнеры в рамках этого compose-проекта так же поднимались вместе, но не "видели" друг друга по сети. В отчете описать, как этого добились и кратко объяснить принцип такой изоляции

## Выполнение работы


Устнавливаем compose

![установка](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D0%BA%D0%BE%D0%BC%D0%BF%D0%BE%D0%B7%D0%B0.jpg)

### Плохой docker compose

Сначала сделаем, а потом подумаем, что натворили (пишем плохой docker compose, у нас он поднимает nginx и MySQL)

```
services:
  web:
    image: nginx
    ports:
      - "8000:80"

  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: "root"
      MYSQL_DATABASE: my_db
      MYSQL_USER: user
      MYSQL_PASSWORD: "password"
    ports:
      - "3306:3306"

```

А теперь запускаем (много букв):

![запуск плохого1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/bad_on)


Страшно, вырубаем...

![остановка плохого](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/bad_off)

### Анализ плохихи практик

Самое время подумать, что было не так...

1. Кажется, кто-то забыл/не подумал, что могут быть разные версии nginx и mysql... По умолчанию используется последняя на данный момент версия. Так лучше не делать - нужно указать конкретную, чтобы избежать неприятных сюрпризов в поведении приложений при обновлении образов.
  
   Запомним: надо указывать версии в хорошем docker compose.

![а-ой](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%B1%D0%B5%D0%B7%D0%BE%D0%BF%D0%B0%D1%81%D0%BD%D0%BE%D1%81%D1%82%D1%8C.jpg)

о чем это мы?.. да, у нас проблемы с безопасностью...

2. Наши сервисы имеют открытые порты, то есть они доступны всем устройствам и пользователям в сети. Получаем уязвимость к атакам => минус безопасность => минус вайб.
   Привяжем порты к локальному хосту.

3. Так-с, а изолировать сети кто будет?.. Да, этого мы пока не сделали, чем это нам грозит? Наши сервисы находятся в одной сети по умолчанию, что может привести к нежелательному взаимодействию между ними.
  
   Так не делаем, делаем по-другому: создаем отдельные сети (db_network и cache_network) для изоляции сервисов. Контейнеры в одной сети не смогут "видеть" или взаимодействовать с контейнерами в другой сети (они изолированы друг от друга). Так мы решим проблему с конфликтами и нежелательными взаимодействиями.

Давайте добавим еще красивую деталь

![секрет](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82.png)

4. Для хранения конфиденциальной информации будем использовать docker secrets

Хранить пароли от рута и пользователя user базы данных в перемнных окружения прямо в файле, конечно, очень хочется, но не стоит - это риск утечки секретных паролей. Переменные окружения можно случайно показать в логах, например. Docker Compose позволяет использовать секреты без необходимости хранить важную информацию в переменных окружения.

Сразу создадим файлы для хранения секретов и запишем туда пароли. Будем надеяться, что они лежат в достаточно надежном месте.

![файл](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82%D1%8B-%D0%BD%D0%B0%D1%87%D0%B0%D0%BB%D0%BE.jpg)

В код добавим такой кусочек:

```
secrets:
  mysql_root_password:
    file: ./secrets/mysql_root_password.txt
  mysql_user_password:
    file: ./secrets/mysql_user_password.txt
```

### Хороший docker compose

А теперь, оглядываясь на все недочеты и предложения по исправлению, пишем хороший (по возможности) docker compose:

```
services:
  web:
    image: nginx:1.21
    ports:
      - "127.0.0.1:8000:80"
    networks:
      - web_network

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/mysql_root_password
      MYSQL_DATABSE: my_db
      MYSQL_USER: user
      MYSQL_PASSWORD_FILE: /run/secrets/mysql_user_password
    networks:
      - db_network
    ports:
      - "127.0.0.1:3306:3306"
    secrets:
      - mysql_root_password
      - mysql_user_password


networks:
  db_network:
    driver: bridge
  web_network:
    driver: bridge

secrets:
  mysql_root_password:
    file: ./secrets/mysql_root_password.txt
  mysql_user_password:
    file: ./secrets/mysql_user_password.txt

```

Запускаем:

![запуск хорошего1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/good_on)


### Изоляция сервисов

Сейчас вернемся к вопросу об изоляции.

У нас была проблема: сервисы находились в одной сети (по умолчанию). Так сервисы могут взаимодействовать друг с другом, а нам бы такого не хотелось.

В хорошем файле мы создали отдельные сети (db_network и cache_network) для изоляции. 
Сервисы db и cache были помещены в соответсвующие сети. Это означает, что контейнеры в одной сети не могут "видеть" или взаимодействовать с контейнерами в другой сети.

Так как Docker использует сети для управления взаимодействием между контейнерами, контейнеры, находящиеся в одной сети, могут общаться друг с другом по именам сервисов. Если они находятся в разных сетях, они изолированы друг от друга (это повышает безопасность и управляемость приложения).

Таким образом, изоляция сервисов позволяет избежать конфликтов и нежелательных взаимодействий.

![final_meme](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%BC%D1%8B%20%D0%B8%20%D0%BB%D0%B0%D0%B1%D0%B0.jpg)


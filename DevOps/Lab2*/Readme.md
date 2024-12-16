# Отчёт по лабораторной работе DevOps2*

## Техническое задание

1. Написать “плохой” Docker compose файл, в котором есть не менее трех “bad practices” по их написанию
2. Написать “хороший” Docker compose файл, в котором эти плохие практики исправлены
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат
4. После предыдущих пунктов в хорошем файле настроить сервисы так, чтобы контейнеры в рамках этого compose-проекта так же поднимались вместе, но не "видели" друг друга по сети. В отчете описать, как этого добились и кратко объяснить принцип такой изоляции

## Выполнение работы


Устнавливаем compose

![установка](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D0%BA%D0%BE%D0%BC%D0%BF%D0%BE%D0%B7%D0%B0.jpg)

Теперь сначала сделаем, а потом подумаем, что натворили (пишем плохой docker compose)

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

Посмотрели, закрываем это чудище:

![остановка плохого](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2*/media/%D0%BE%D1%82%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D0%B5%20%D0%BF%D0%BB%D0%BE%D1%85%D0%BE%D0%B3%D0%BE.jpg) 

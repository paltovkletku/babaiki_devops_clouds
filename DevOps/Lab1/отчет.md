# Отчёт по лабораторной работе DevOps1

## Возвращение Линукса. Знакомство с Nginx

### Техническое задание:
Настроить Nginx таким образом, чтобы выполнялись следующие требования
1. Подключение осуществлялось через протокол https с сертификатом;
2. Настроить принудительное перенаправление HTTP-запросов (порт 80) на HTTPS (порт 443) для обеспечения безопасного соединения;
3. Использовать alias для создания псевдонимов путей к файлам или каталогам на сервере;
4. Настроить виртуальные хосты для обслуживания нескольких доменных имен на одном сервере.

### Давайте постепенно разбираться

#### Устанавливаем Nginx
Обновим пакеты и установим Nginx, используя команды
```bash
sudo apt-get update
```
![Обновление пакетов](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/apt-get.jpg)

```bash
sudo apt install nginx
```
![Установка Nginx](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/install%20nginx.jpg)

Проверяем подключение Nginx чреез ip-адрес:
![Рабочий nginx через ip](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/welcome.jpg)

#### Создаем файлики, с которыми будем работать

При помощи mkdir создаем две директории: /var/www/imfine и /var/www/imreallyfine (скринов нет потому что у нас лапки)

Внутри директорий создаем файлики index.html, это будующие конфиги.
Открываем файлики:
```bash
sudo nano /etc/nginx/sites-available/imfine
```
```bash
sudo nano /etc/nginx/sites-available/imreallyfine
```
Теперь заполняем их:
![Содержимое конфига](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/configs%201.png)

#### Настраиваем

Чтобы nginx смог обслуживать наши сайты мы создаем символические ссылки на файлы конфигурации imfine и imreallyfine в /etc/nginx/sites-enabled/.
```bash
sudo ln -s /etc/nginx/sites-available/imfine  /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/imreallyfine  /etc/nginx/sites-enabled/
```

Затем рестартим nginx с помощью команды
```bash
sudo service nginx restart
```

Настраивать связь с DNS-сервером не входило в наши планы, а потому в хосты на нашем компьютере добавим домены сайтов. В файле /etc/hosts пишем айпи нашего сервера и требуемые домены: imfine.local и imreallyfine.local.

![Хосты](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/hosts.png)

Теперь откроем наши странички и посмотрим.

![imfine](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/imfine%20http.jpg)
![imreallyfine](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/imreallyfine%20http.jpg)

На этом этапе наши сайты уже открываются через http.

#### Работаем с сертификатами

Разумеется, чтобы получить хороший сертификат от центра сертификации, нужно сгенерировать запрос на подпись сертификата, выбрать центр сертификации, отправить туда запрос. И после проверки мы сможем получить и установить сертификат.

Это, конечно, все круто и здорово. Но мы пойдем другим путем, а именно сделаем самоподписанные сертификаты)

Генерируем самоподписанные сертификаты сроком действия на 365 дней и закрытые ключи к ним, ключи не шифруем с надеждой на то, что наши сайты никому не нужны...(пожалуйста не взламывайте, мы и так скажем всю информацию добровольно)

![Генерация сертификата и ключа](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/certificates%20and%20keys.jpg)

#### Перенаправление запросов

Теперь редактируем наши конфиги. Нам нужно принудительно перенаправить запросы с 80 порта на 443.

![Конфиг https1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/imfine%20https.jpg)

![Конфиг https2](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/imreallyfine%20https.jpg)

И вииииидим:

![Сюда ходить опасно](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/warning.jpg)

Но нас это нее останавливает...

![Но мы пойдем сюда](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/not%20stop.jpg)

Поэтому...

![Все работает 1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/https%20working2.jpg)

![Все работает 2](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/https%20working1.jpg)

#### Настраиваем alias

При помощи mkdir создаем еще больше каталогов внутри каталогов. В последних создаем еще по одному html-файлику.

Теперь добавляем alias в конфиги:

![alias1](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/alias_conf1.png)

![alias2](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/alias_conf2.png)

Мы создали псевдонимы, теперь откроем сайты

![i love labs](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/i%20love%20labs.jpg)

![i love linux](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/i%20love%20linux.jpg)

Наша лаба готова))) уррааа, мы отдыхать

А чтобы было веселее читать отчет, листайте дальше

![i love linux](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/i%20love%20linux.jpg)


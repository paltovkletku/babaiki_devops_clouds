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

Внутри директорий создаем файлики happy.html, это будующие конфиги.
Теперь заполняем их:
![Содержимое конфига](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab1/configs%201.png)






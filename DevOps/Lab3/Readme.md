# Отчёт по лабораторной работе DevOps3

## Техническое задание
1. Написать “плохой” CI/CD файл, который работает, но в нем есть не менее пяти “bad practices” по написанию CI/CD;
2. Написать “хороший” CI/CD, в котором эти плохие практики исправлены;
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат.

## CI/CD - что это?..

CI - Continuous Integration

CD - Continuous Delivery

Как мы поняли, ci/cd - это подход, в котором мы выстраиваем процесс разработки программ так, чтобы можно было регулярно вливать код с новыми фичами в разрабатываемый проект, а затем все это должно автоматически собираться, тестироваться и развертываться.

CI: сливаем свой код в общий репозиторий, у нас запускается автоматическая сборка, различные валидаторы и тесты, чтобы проверить, что код успешно интегрируется с текущим кодом.

CD: доставляем фичу до конечного пользователя.

## 2 Плохой ci/cd

```
name: Bad CI/CD #название файла

on: #триггер, запускающий работы - пуш в ветку main
  push:
    branches:
      - main

jobs: #работы 
  build-and-deploy: #имя работы
    runs-on: ubuntu-latest #на чем запускаем
    steps: # теперь описываем пошаговый план работы
      - name: Checkout #названия шагов
        uses: actions/checkout@v3
#действие, используемое на шаге (чем отличается от run?). Конкретно здесь: checks out your repository code in the workflow environment
# Используя uses, вы по сути говорите: «Эй, я хочу использовать это готовое действие для выполнения определенной задачи». usesКлючевое слово позволяет вам использовать возможности повторно используемых действий, что может упростить ваш рабочий процесс и сократить усилия по обслуживанию.

      - name: Install deps
        run: npm install # запуск команды Здесь: устанавливаем зависимости

      - name: Test
        run: npm run test #запускаем тесты для проекта

      - name: Build
        run: npm run build # сборка

      - name: Deploy
        run: |
           rsync -avz --delete ./dist/ user@server_ip:/var/www/html/


Using --force with npm install: This can lead to unexpected behavior and dependencies being installed with incorrect versions.
Using --prod with npm run build: This can lead to unexpected behavior and the build process not being reproducible, щас бы показывать где все файлы лежат (hardcode), install npm
```

Bad practices:
1. Использование ubuntu:latest. latest подразумевает использование последнего доступного образа ubuntu, то есть он может меняться со временем, что может вызвать проблемы.
2. отсутствует обработка ошибок. Команды run не обрабатывают ошибки, если команда не выполняется, конвейер продолжит выполняться без остановки.
3. Отсутствие кеширования. Без кэширования при каждом запуске все строится с нуля, загружаются и переустанавливаются зависимости. Это может привести к замедлению времени сборки, увеличению затрат.
4. Зависимости установлены без уточнения версии. Установка разных версий зависимостей при каждом запуске может привести к непредвиденному поведению или сбоям в рабочем процессе.
5. Build и deploy объединены. Объединяя сборку и развертывание, мы тесно связываем две отдельные проблемы. Это затрудняет поддержку, обновление или отладку отдельных шагов без влияния на весь процесс. Наличие отдельных заданий для сборки и развертывания обеспечивает большую гибкость




![картинка](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2/images/docker%20running.jpg)



## Хороший ci/cd

```
name: Good CI/CD

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-20.04
    steps: 
      - name: Checkout
        uses: actions/checkout@v3

        - name: Cache dependencies
        uses: actions/cache@v2
        id: cache
        cache: npm
        path: ~/.npm

        - name: Install deps
        uses: actions/npm-install@v1
        cache: ${{ steps.cache.outputs.cache }}
        npm-version: '14.17.0'

      - name: Install deps
        run: npm ci

      - name: Test
        run: npm run test

      - name: Build
        run: npm run build

  deploy:
    runs-on: ubuntu-20.04
    needs: build
    steps:
      - name: Deploy
        env:
          SERVER_IP: ${{ secrets.SERVER_IP }}
          USERNAME: ${{ secrets.USERNAME }}
          DEPLOYMENT_PATH: ${{ secrets.DEPLOYMENT_PATH }}
        run: |
          rsync -avz --delete ./dist/ $USERNAME@$SERVER_IP:$DEPLOYMENT_PATH
```

Изменения:
1. Разделение build и deploy. Разделив сборку и развертывание на отдельные задания, мы обеспечиваем более надежный, гибкий и масштабируемый конвейер CI/CD.
2. Теперь будет всегда использоваться Ubuntu 20.04 - определенная версия образа ubuntu, которая не изменится без нашего ведома.
3. Добавление кэширования.
4. Установление зависимостей.
5. Определение переменных окружения.

## 4


## 5

Выполнили: Борисевич Анна, Ходакова Мария.


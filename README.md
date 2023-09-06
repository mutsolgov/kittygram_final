# Социальная сеть Kittygram
![example workflow](https://github.com/mutsolgov/kittygram_final/actions/workflows/main.yml/badge.svg)

## Kittygram: это социальная сеть, с возможностью обмена фотографий, достижений, а также возроста кошек.

### Стэк

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) ![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB) ![Django](https://img.shields.io/badge/django-%23092E20.svg?style=for-the-badge&logo=django&logoColor=white) ![Postgres](https://img.shields.io/badge/postgres-%23316192.svg?style=for-the-badge&logo=postgresql&logoColor=white) ![DjangoREST](https://img.shields.io/badge/DJANGO-REST-ff1709?style=for-the-badge&logo=django&logoColor=white&color=ff1709&labelColor=blue) ![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)  ![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white) ![Gunicorn](https://img.shields.io/badge/gunicorn-%298729.svg?style=for-the-badge&logo=gunicorn&logoColor=white) ![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)   ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white) ![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)


___
___
## Установка проекта.

1. Клонируем репозиторий:

    ```
    git clone git@github.com:mutsolgov/kittygram_final.git
    ```
2. Переходим в папку с проектом:
    ```
    cd kittygram_final
    ```
2. Создаем файл .env и заполням его своими данными. Перечень данных указан в корневой директории проекта в файле .env.example.

___
### Создание Docker-образов

1.  Замените username на ваш логин на DockerHub:

    ```
    cd frontend
    docker build -t username/kittygram_frontend .
    cd ../backend
    docker build -t username/kittygram_backend .
    cd ../nginx
    docker build -t username/kittygram_gateway . 
    ```
2. Проверьте, что на вашем компьютере созданы необходимые образы, — выполните команду: 
    ``` 
    docker image ls
    ``` 

5. Загружаем образы на DockerHub:

    ```
    docker push username/kittygram_frontend
    docker push username/kittygram_backend
    docker push username/kittygram_gateway
    ```

___
### Деплой на сервере

1. Подключитесь к удаленному серверу

    ```
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
    ```

2. Создаем на сервере директорию kittygram через терминал

    ```
    mkdir kittygram
    ```


3. В директорию kittygram/ скопируйте файлы docker-compose.production.yml и .env:

    ```
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
    * path_to_SSH — путь к файлу с SSH-ключом;
    * SSH_name — имя файла с SSH-ключом (без расширения);
    * username — ваше имя пользователя на сервере;
    * server_ip — IP вашего сервера.
    ```

4. Запустите docker compose в режиме демона:

    ```
    sudo docker compose -f docker-compose.production.yml up -d
    ```

5. Выполните миграции, соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:

    ```
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /static/static/
    ```

6. На сервере в редакторе nano откройте конфиг Nginx:

    ```
    sudo nano /etc/nginx/sites-enabled/default
    ```

7. Измените настройки location в секции server:

    ```
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

8. Проверьте работоспособность конфига Nginx:

    ```bash
    sudo nginx -t
    ```

9.  Перезапускаем Nginx
    ```bash
    sudo service nginx reload
    ```

___
### Настройка CI/CD

1. Файл workflow уже написан. Он находится в директории

    ```
    kittygram/.github/workflows/main.yml
    ```

2. Для адаптации его на сервере добавьте секреты в GitHub Actions:

    ```bash
    DOCKER_USERNAME                # имя пользователя в DockerHub
    DOCKER_PASSWORD                # пароль пользователя в DockerHub
    HOST                           # ip_address сервера
    USER                           # имя пользователя
    SSH_KEY                        # приватный ssh-ключ (cat ~/.ssh/id_rsa)
    SSH_PASSPHRASE                 # кодовая фраза (пароль) для ssh-ключа

    TELEGRAM_TO                    # id телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
    TELEGRAM_TOKEN                 # токен бота (получить токен можно у @BotFather, /token, имя бота)
    ```

___

### Автор
## Муцольгов Магомед
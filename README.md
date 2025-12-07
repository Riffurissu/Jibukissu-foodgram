Находясь в папке infra, выполните команду docker-compose up. При выполнении этой команды контейнер frontend, описанный в docker-compose.yml, подготовит файлы, необходимые для работы фронтенд-приложения, а затем прекратит свою работу.

По адресу http://localhost изучите фронтенд веб-приложения, а по адресу http://localhost/api/docs/ — спецификацию API.

## Скачивание проекта с GitHubЗапуск проекта из образов с Docker Hub

При первом запуске убедитесь, что Docker есть и работает:

```bash
sudo systemctl status docker
```

Для запуска необходимо скопировать данный репозиторий и перейти в созданную директорию:

```bash
git clone https://github.com/Riffurissu/Jibukissu-foodgram.git
```
```bash
cd Jibukissu-foodgram
```

## Настройки переменных окружения (о том, как заполнить env)

В корне проекта следует создать файл `.env` и заполнить по образу из файла `.env.example`

```bash
cp .env.example .env
```

## Запуск проекта из образов с Docker Hub

В папке проекта запускаем файл `docker-compose.production.yml`:

```bash
sudo docker compose -f docker-compose.production.yml up -d
```

Далее автоматически скачаются образы, произойдет создание и включение контейнеров и объедение их в одну сеть.

## После запуска: Миграции, сбор статики

После запуска необходимо выполнить миграции бэкенда и сбор статики.
Статики фронтенда собирается во время запуска контейнера, после чего он останавливается. 

```bash
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
```
```bash
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
```
```bash
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```

После этого проект будет доступен по адресам:
```
localhost:8080
127.0.0.1:8080
<внешний адрес>:8080
```

## Создание суперпользователя (админа):
```bash
sudo docker compose -f docker-compose.production.yml exec backend python manage.py createsuperuser noinput
```

## На случай, если нужно наполнение тегами и ингредиентами:

После первого развёртывания для работы с рецептами нужны будут теги и ингредиенты.
Предусмотрено начальное наполнение этих таблиц в базе командой:
```bash
sudo docker compose -f docker-compose.production.yml exec backend python manage.py data_loader
```

## Остановка проекта:

```bash
sudo docker compose -f docker-compose.production.yml down
```
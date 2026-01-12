
## 🔧 Docker Compose команды (альтернатива infra.sh)

```bash
# Запуск
docker-compose up -d

# Остановка
docker-compose down

# Перезапуск
docker-compose restart

# пересобрать образ (local)
docker-compose -f docker-compose.local.yaml build api --no-cache
# пересобрать образ (local) c кешем
docker-compose -f docker-compose.local.yaml  up -d --build sso

# Пересоздание контейнеров
docker-compose up -d --force-recreate

# Просмотр логов
docker-compose logs -f [service]

# Список контейнеров
docker-compose ps

# Выполнить команду в контейнере
docker-compose exec sso sh
```

## 🐛 Отладка

```bash
# Войти в контейнер
docker-compose exec sso sh
docker-compose exec api sh
docker-compose exec frontend sh

# Проверить сеть
docker network ls
docker network inspect my_app_network

# Проверить volumes
docker volume ls

# Проверить образы
docker images | grep ghcr.io

# Принудительно пересоздать контейнер
docker-compose up -d --force-recreate sso

# Проверить использование ресурсов
docker stats
```

## 🌐 Nginx

```bash
# Перезагрузить конфигурацию nginx без перезапуска
docker-compose exec nginx nginx -s reload

# Проверить конфигурацию
docker-compose exec nginx nginx -t

# Просмотр логов nginx
docker-compose logs -f nginx
```

## 📊 Мониторинг

```bash
# Статус всех контейнеров
docker-compose ps

# Использование ресурсов
docker stats --no-stream

# Размер образов
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Логи за последние 100 строк
docker-compose logs --tail=100

# Следить за логами в реальном времени
docker-compose logs -f --tail=50
```

## 🔄 Обновление и деплой

```bash
# На production сервере
cd /root/apps/my-project

# Загрузить новые образы
docker-compose pull

# Перезапустить с новыми образами
docker-compose up -d
```

## 🔐 Работа с Registry

```bash
# Логин в GitHub Container Registry
echo "ghp_token" | docker login ghcr.io -u username --password-stdin

# Посмотреть образы в registry
# Зайдите на: https://github.com/YOUR_USERNAME?tab=packages

# Вручную загрузить образ
docker pull ghcr.io/YOUR_USERNAME/sso:latest

# Вручную отправить образ
docker push ghcr.io/YOUR_USERNAME/sso:latest
```

### Хочу посмотреть что происходит внутри контейнера:

```bash
docker-compose exec sso sh
ls -la
cat config/prod-docker.yaml
ps aux
```

### Нужно изменить конфиг nginx:

```bash
# Отредактируйте nginx/nginx.conf
nano nginx/nginx.conf

# Перезагрузите конфигурацию
docker-compose exec nginx nginx -t  # проверка
docker-compose exec nginx nginx -s reload  # применение
```


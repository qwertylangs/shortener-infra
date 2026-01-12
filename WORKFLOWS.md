# Workflow файлы для сервисов

Эти файлы нужно добавить в каждый репозиторий сервиса (sso, url-shortener, shortener-front)

## Для SSO сервиса

Создайте файл `.github/workflows/build-and-deploy.yaml` в репозитории `sso`:

```yaml
name: Build and Deploy SSO

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ghcr.io/${{ github.repository_owner }}/sso
          tags: |
            type=raw,value=latest
            type=sha,prefix={{branch}}-
      
      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          cache-from: type=registry,ref=ghcr.io/${{ github.repository_owner }}/sso:buildcache
          cache-to: type=registry,ref=ghcr.io/${{ github.repository_owner }}/sso:buildcache,mode=max
      
      - name: Trigger infrastructure deployment
        if: github.event_name == 'push'
        uses: peter-evans/repository-dispatch@v2
        with:
          token: ${{ secrets.PAT_TOKEN }}
          repository: YOUR_USERNAME/shortener-infra
          event-type: sso-updated
```

## Для API сервиса

Создайте файл `.github/workflows/build-and-deploy.yaml` в репозитории `url-shortener`:

```yaml
name: Build and Deploy API

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ghcr.io/${{ github.repository_owner }}/url-shortener
          tags: |
            type=raw,value=latest
            type=sha,prefix={{branch}}-
      
      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          cache-from: type=registry,ref=ghcr.io/${{ github.repository_owner }}/url-shortener:buildcache
          cache-to: type=registry,ref=ghcr.io/${{ github.repository_owner }}/url-shortener:buildcache,mode=max
      
      - name: Trigger infrastructure deployment
        if: github.event_name == 'push'
        uses: peter-evans/repository-dispatch@v2
        with:
          token: ${{ secrets.PAT_TOKEN }}
          repository: YOUR_USERNAME/shortener-infra
          event-type: api-updated
```

## Для Frontend сервиса

Создайте файл `.github/workflows/build-and-deploy.yaml` в репозитории `shortener-front`:

```yaml
name: Build and Deploy Frontend

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ghcr.io/${{ github.repository_owner }}/shortener-front
          tags: |
            type=raw,value=latest
            type=sha,prefix={{branch}}-
      
      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          cache-from: type=registry,ref=ghcr.io/${{ github.repository_owner }}/shortener-front:buildcache
          cache-to: type=registry,ref=ghcr.io/${{ github.repository_owner }}/shortener-front:buildcache,mode=max
      
      - name: Trigger infrastructure deployment
        if: github.event_name == 'push'
        uses: peter-evans/repository-dispatch@v2
        with:
          token: ${{ secrets.PAT_TOKEN }}
          repository: YOUR_USERNAME/shortener-infra
          event-type: frontend-updated
```

## Важно!

1. Замените `YOUR_USERNAME` на ваш GitHub username
2. Добавьте `PAT_TOKEN` в GitHub Secrets каждого репозитория
3. PAT_TOKEN должен иметь права `repo` и `workflow`
4. GITHUB_TOKEN создается автоматически и имеет права на запись в registry

## Создание PAT_TOKEN

1. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate new token (classic)
3. Выберите scopes: `repo`, `workflow`
4. Скопируйте токен
5. Добавьте его в Secrets каждого репозитория как `PAT_TOKEN`


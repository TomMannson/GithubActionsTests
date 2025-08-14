# 🐳 Docker Setup Guide

## 📋 Wymagania

Aby workflow mógł pushować obrazy do Docker Hub, musisz skonfigurować następujące secrets w GitHub:

### 1. Docker Hub Secrets

Przejdź do `Settings` → `Secrets and variables` → `Actions` w Twoim repozytorium i dodaj:

- **`DOCKERHUB_USERNAME`** - Twoja nazwa użytkownika Docker Hub
- **`DOCKERHUB_TOKEN`** - Token dostępu Docker Hub (nie hasło!)

### 2. Jak utworzyć Docker Hub Token

1. Zaloguj się na [Docker Hub](https://hub.docker.com)
2. Przejdź do `Account Settings` → `Security`
3. Kliknij `New Access Token`
4. Nadaj nazwę (np. "GitHub Actions")
5. Skopiuj token i dodaj go jako secret `DOCKERHUB_TOKEN`

## 🚀 Użycie Workflow

### Automatyczne uruchomienie
Workflow uruchamia się automatycznie przy:
- Push na `main`/`master` branch
- Push tagów `v*` (np. `v1.0.0`)
- Pull Request na `main`/`master`

### Ręczne uruchomienie
1. Przejdź do `Actions` → `Build and Push Docker Image`
2. Kliknij `Run workflow`
3. Wybierz branch i kliknij `Run workflow`

## 🖥️ Multi-Platform Support

Workflow buduje obrazy dla następujących platform:
- **Linux**: `amd64`, `arm64`
- **macOS**: `amd64`, `arm64`

Dzięki temu możesz uruchamiać aplikację:
- Lokalnie na MacOS (Intel/Apple Silicon)
- Na serwerach Linux (Intel/ARM)

## 📦 Obrazy Docker

### Naming Convention
- **Repository**: `tommannson/github-actions-app`
- **Tags**: Automatycznie generowane na podstawie:
  - Branch name
  - Git SHA
  - Semantic version (z tagów)
  - `latest` dla głównego branch

### Przykłady tagów
- `main-abc1234` - dla commit na main branch
- `v1.0.0` - dla tagu v1.0.0
- `1.0` - dla major.minor wersji
- `latest` - zawsze najnowsza wersja

## 🔧 Konfiguracja JIB

JIB jest skonfigurowany w `build.gradle` z:
- **Base image**: `eclipse-temurin:21-jre-alpine`
- **Port**: `8080`
- **Environment**: `SPRING_PROFILES_ACTIVE=prod`
- **Labels**: Metadata obrazu (wersja, opis, data utworzenia)

## 🐛 Troubleshooting

### Błąd autoryzacji
- Sprawdź czy `DOCKERHUB_USERNAME` i `DOCKERHUB_TOKEN` są poprawnie ustawione
- Upewnij się, że token ma uprawnienia do pushowania

### Błąd budowania
- Sprawdź czy projekt kompiluje się lokalnie: `./gradlew build -x test`
- Sprawdź logi workflow w GitHub Actions

### Błąd push
- Sprawdź czy masz uprawnienia do pushowania do repozytorium Docker Hub
- Upewnij się, że repozytorium `tommannson/github-actions-app` istnieje

## 📚 Przydatne komendy

### Lokalne testowanie
```bash
# Budowanie bez testów
./gradlew build -x test

# Budowanie obrazu lokalnie (bez push)
./gradlew jibDockerBuild

# Uruchomienie lokalnie
docker run -p 8080:8080 tommannson/github-actions-app:latest
```

### Sprawdzanie obrazów
```bash
# Lista lokalnych obrazów
docker images | grep github-actions-app

# Informacje o obrazie
docker inspect tommannson/github-actions-app:latest

# Uruchomienie z shell
docker run -it --rm tommannson/github-actions-app:latest sh
```

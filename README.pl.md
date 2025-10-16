# Stos AI
Kompleksowy stos AI zawierający proxy LiteLLM, Ollama, Open-WebUI, bazę danych PostgreSQL i monitoring Prometheus.

## Przegląd usług
- **LiteLLM Proxy** - Brama API dla wielu dostawców LLM (port 4000)
- **Ollama** - Lokalne środowisko uruchomieniowe LLM z zarządzaniem modelami (port 11434)
- **Open-WebUI** - Interfejs webowy do interakcji z modelami AI (port 3000)

Usługi wspierające:
- **PostgreSQL** - Baza danych do przechowywania danych LiteLLM (port 5432)
- **Prometheus** - Zbieranie metryk i monitoring (port 9090)

## Wymagania wstępne
- Docker i Docker Compose
- Absolutne minimum to 8GB RAM (16GB+ lub więcej jest zdecydowanie zalecane)
- Obsługa GPU (zalecana karta graficzna nVidia lub AMD dla lepszej wydajności)

## Szybki start

### 1. Klonowanie i konfiguracja
```bash
git clone <adres-repozytorium>
cd ai_stack
```

### 2. Utworzenie pliku środowiskowego
Utwórz plik `.env` w katalogu głównym z następującymi zmiennymi:
```env
LITELLM_MASTER_KEY="sk-[wpisz tutaj 10-16 losowych cyfr]"
LITELLM_SALT_KEY="sk-[wpisz tutaj 10-16 losowych cyfr]"
```
**LITELLM_MASTER_KEY** to Twoje początkowe hasło **administratora** dla **liteLLM**

### 3. Konfiguracja bezpieczeństwa
**WAŻNE**: Zmień domyślne hasła do bazy danych w `docker-compose.yml`:
```yaml
# W sekcji usługi db zmień:
POSTGRES_PASSWORD: TWOJE_BEZPIECZNE_HASŁO
# W sekcji usługi proxy zaktualizuj DATABASE_URL:
DATABASE_URL: "postgresql://llmproxy:TWOJE_BEZPIECZNE_HASŁO@db:5432/litellm"
```

### 4. Uruchomienie stosu
```bash
docker-compose up -d
```

### 5. Weryfikacja usług
Sprawdź, czy wszystkie usługi działają:
```bash
docker-compose ps
```

## Dostęp do usług
- **Open-WebUI**: http://localhost:3000
- **LiteLLM Proxy**: http://localhost:4000
- **Prometheus**: http://localhost:9090
- **Ollama API**: http://localhost:11434
- **PostgreSQL**: localhost:5432

## Konfiguracja

### Modele LiteLLM
System jest skonfigurowany do automatycznego proxy wszystkich modeli Ollama przez LiteLLM. Modele są konfigurowane w `litellm_config.yaml`.

### Modele Ollama
Pobierz modele używając usługi Ollama:
```bash
docker exec ollama ollama pull qwen3:latest
docker exec ollama ollama pull gemma3:latest
```

### Obsługa GPU
Konfiguracja zawiera obsługę GPU dla kart AMD/Intel. Dla kart NVIDIA odkomentuj odpowiednie sekcje w `docker-compose.yml`.

## Trwałość danych
- Modele Ollama: `./ollama/ollama`
- Dane Open-WebUI: `./data/open-webui`
- Dane PostgreSQL: Wolumin Docker `litellm_postgres_data`
- Dane Prometheus: Wolumin Docker `prometheus_data`

## Monitoring
Prometheus jest skonfigurowany do zbierania metryk z proxy LiteLLM. Dostęp do interfejsu webowego Prometheus pod adresem http://localhost:9090.

## Rozwiązywanie problemów

### Typowe problemy
1. **Konflikty portów**: Upewnij się, że porty 3000, 4000, 5432, 9090 i 11434 są dostępne
2. **Problemy z połączeniem do bazy danych**: Zweryfikuj, czy DATABASE_URL w pliku `.env` odpowiada poświadczeniom bazy danych
3. **Dostęp do GPU**: Upewnij się, że Twój użytkownik należy do grupy `docker` i ma dostęp do `/dev/dri`

### Logi
Wyświetl logi usługi:
```bash
docker-compose logs [nazwa-usługi]
```

### Sprawdzanie stanu
Stos zawiera kontrole stanu dla kluczowych usług. Sprawdź stan usługi:
```bash
docker-compose ps
```

## Uwagi dotyczące bezpieczeństwa
- Zmień wszystkie domyślne hasła przed wdrożeniem produkcyjnym
- Rozważ użycie zmiennych środowiskowych dla wrażliwej konfiguracji
- Regularnie aktualizuj obrazy kontenerów w celu uzyskania poprawek bezpieczeństwa
- Ogranicz dostęp sieciowy tylko do wymaganych portów

## Rozwój
Aby zmodyfikować konfigurację:
1. Edytuj odpowiednie pliki konfiguracyjne (`docker-compose.yml`, `litellm_config.yaml`, itp.)
2. Zrestartuj dotknięte usługi: `docker-compose restart [nazwa-usługi]`

## Współpraca
Upewnij się, że wszelkie zmiany zachowują najlepsze praktyki bezpieczeństwa i odpowiednio aktualizuj ten plik README.
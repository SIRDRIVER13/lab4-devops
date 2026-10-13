# Lab 4 – Automatyzacja deploymentu (Task API)

Prosta aplikacja webowa (Flask + PostgreSQL) z automatycznym wdrożeniem:
Docker → docker compose → GitHub Actions (build, test, deploy) → Kubernetes (kind).

## Endpointy

| Metoda | Ścieżka | Opis |
| --- | --- | --- |
| GET | `/` | informacje o aplikacji i wersji |
| GET | `/health` | stan aplikacji i połączenia z bazą |
| GET | `/tasks` | lista zadań |
| POST | `/tasks` | nowe zadanie, body: `{"title": "..."}` |
| PATCH | `/tasks/<id>` | oznaczenie zadania, body: `{"done": true}` |

## Lokalnie

    docker build -t task-api:local .
    docker compose up -d --build
    curl localhost:8000/health
    docker compose down

## Testy

    pip install -r requirements-dev.txt
    pytest -v                                                     # jednostkowe
    BASE_URL=http://localhost:8000 pytest -o addopts="" tests/integration   # integracyjne

## CI/CD

`.github/workflows/ci-cd.yml` – po każdym pushu do `main`:
1. **Build** – obraz Docker otagowany SHA commita,
2. **Test** – testy jednostkowe + integracyjne na docker compose z PostgreSQL,
3. **Deploy** – klaster Kubernetes (kind), manifesty z `k8s/`, smoke test, rolling update bez przestojów,
4. **Powiadomienie** – status w podsumowaniu pipeline (opcjonalnie Slack przez sekret `SLACK_WEBHOOK_URL`).

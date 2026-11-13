# ДЗ_1 — Quality Bar Proposal для PayPilot

Домашнє завдання №1 курсу AI Quality Engineering: вхід з L01 і Quality Bar Proposal за результатами лабораторної L02.

## Файли

| Файл | Що це |
| --- | --- |
| `quality-bar-proposal.md` | документ, що здається: розділ 0 (вхід з L01) і розділи 1–7 |
| `reports/l02-clean-lesson-02-20261113-105418.json` | сирі дані прогону, з якого взято всі числа |
| `reports/full-run.txt` | текстовий вивід того самого прогону |
| `cases.json`, `l02_eval.py`, `requirements.txt` | кейси й скрипт, на яких зроблено прогін |
| `specification-review.md`, `base.v1.1.md`, `prompts/CHANGELOG.md`, `prompt-governance-policy.md` | самостійне завдання L01 (окремо не здається) |

## Як відтворити прогін

Потрібні запущений стенд PayPilot (`paypilot-stand`, `localhost:8000`) із `CLOCK_OVERRIDE=2026-09-15T10:00:00Z` і ключ Anthropic у `.env` цього репозиторію (`ANTHROPIC_API_KEY`).

```bash
cp .env.example .env        # вписати ANTHROPIC_API_KEY
docker compose build
mkdir -p reports
docker compose run --rm -T eval --runs 3 --baseline-runs 2 2>&1 | tee reports/full-run.txt
```

## Параметри прогону

- Профілі: `clean` × 2 (baseline), `lesson-02` × 3 (дефекти D04, D05, D16, D19, D20, D25).
- Модель судді: `claude-haiku-5-5` (Anthropic).
- Агент: стенд PayPilot, провайдер Anthropic.
- Годинник: `CLOCK_OVERRIDE=2026-09-15T10:00:00Z`.
- Дата прогону: 13.11.2026.

`.env` і API-ключі в репозиторій не додаються.

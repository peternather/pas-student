# Dáta ShopIT

Všetky tabuľky sú vygenerované simuláciou (fiktívna firma), ale správajú sa ako reálne dáta. Obdobie: **5. 1. 2026 – 29. 3. 2026**. Formát CSV, oddeľovač čiarka, desatinná bodka, časy v miestnom čase bez časovej zóny.

Načítanie v Colabe:

```python
import pandas as pd
DATA = "https://raw.githubusercontent.com/<ORG>/pas/main/data/shopit/"   # nastaví vyučujúci
sessions = pd.read_csv(DATA + "sessions.csv", parse_dates=["start_ts"])
```

## `sessions.csv` – návštevy webu (60 000 riadkov)

| Stĺpec | Typ | Popis |
|---|---|---|
| `session_id` | text | identifikátor návštevy |
| `user_id` | text | identifikátor používateľa (ten istý človek môže prísť viackrát) |
| `start_ts` | čas | začiatok návštevy |
| `device` | kategória | `desktop`, `mobile`, `tablet` |
| `country` | kategória | `SK`, `CZ`, `HU`, `AT`, `PL` |
| `source` | kategória | odkiaľ prišiel: `organic`, `ads`, `direct`, `email`, `social` |
| `products_viewed` | celé číslo | počet prezretých produktov |
| `duration_s` | celé číslo | dĺžka návštevy v sekundách |
| `purchased` | 0/1 | či návšteva skončila nákupom |
| `basket_value_eur` | číslo | hodnota nákupu v €, prázdne ak nenakúpil |

## `requests.csv` – access log (150 000 riadkov, náhodná vzorka)

| Stĺpec | Typ | Popis |
|---|---|---|
| `ts` | čas | čas requestu |
| `session_id` | text | návšteva, ku ktorej request patrí (prázdne pri botoch) |
| `client` | kategória | `browser` (človek) alebo `bot` (crawler) |
| `endpoint` | kategória | `/`, `/product`, `/search`, `/cart`, `/checkout`, `/api/recommend` |
| `server` | kategória | `web-1` … `web-4` |
| `status` | celé číslo | HTTP status (200, 304, 404, 500, 504) |
| `latency_ms` | číslo | doba odozvy v ms, prázdne pri timeoute (status 504) |
| `response_kb` | číslo | veľkosť odpovede v kB |

## `ab_checkout.csv` – A/B test nového checkoutu (24 000 riadkov)

Test prebiehal 9. – 22. 3. 2026. Varianta `A` je pôvodný checkout, `B` nový.

| Stĺpec | Typ | Popis |
|---|---|---|
| `session_id`, `user_id` | text | identifikátory |
| `ts` | čas | čas návštevy |
| `device` | kategória | `desktop`, `mobile` |
| `variant` | kategória | `A`, `B` |
| `converted` | 0/1 | či nakúpil |
| `order_value_eur` | číslo | hodnota objednávky, prázdne ak nenakúpil |

## `metrics_hourly.csv` – monitoring (2 016 riadkov)

| Stĺpec | Typ | Popis |
|---|---|---|
| `hour` | čas | začiatok hodiny |
| `requests` | celé číslo | počet requestov za hodinu |
| `errors` | celé číslo | počet chybových odpovedí (5xx) |
| `error_rate` | číslo | `errors / requests` |
| `cpu_pct` | číslo | priemerné vyťaženie CPU v % |
| `p95_latency_ms` | číslo | 95. percentil latencie v ms |

## `benchmark.csv` – benchmark backendu (1 800 riadkov)

Každý stroj (`m1`–`m6`) spustil každú verziu 5-krát (`run`), každý beh má 30 iterácií.

| Stĺpec | Typ | Popis |
|---|---|---|
| `version` | kategória | `v1`, `v2` |
| `machine` | kategória | `m1` … `m6` |
| `run` | celé číslo | číslo behu 1–5 |
| `iteration` | celé číslo | poradie iterácie v behu 0–29 |
| `latency_ms` | číslo | čas spracovania testovacej požiadavky v ms |

## `customers.csv` – zákazníci (6 000 riadkov)

| Stĺpec | Typ | Popis |
|---|---|---|
| `customer_id` | text | identifikátor |
| `tenure_days` | celé číslo | ako dlho je zákazníkom (dni) |
| `orders_12m` | celé číslo | počet objednávok za posledných 12 mesiacov |
| `days_since_last_order` | celé číslo | dni od poslednej objednávky |
| `avg_order_value_eur` | číslo | priemerná hodnota objednávky |
| `support_tickets_90d` | celé číslo | počet tiketov na podporu za posledných 90 dní |
| `newsletter` | 0/1 | odoberá newsletter |
| `main_device` | kategória | najčastejšie zariadenie |
| `churned_90d` | 0/1 | či v nasledujúcich 90 dňoch odišiel (nič nekúpil) |

## Generovanie

Dáta sa generujú deterministicky (seed 2026):

```bash
cd data/generator
../../.venv/bin/python -m shopit_data.generate ../shopit
../../.venv/bin/pytest -q          # overí, že dáta obsahujú zamýšľané efekty
```

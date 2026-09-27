<div align="center">

# Hi, I'm azzyth ദ്ദി(˵ •̀ ᴗ - ˵ ) ✧

**Data Science student @ Telkom University** · Kaggle competitor · Machine Learning & Deep Learning enthusiast

[![GitHub followers](https://img.shields.io/github/followers/azzyth?style=social)](https://github.com/azzyth)

</div>

---

## About Me ˙𐃷˙

I'm a Data Science student at **Telkom University** who loves turning raw data into working models — and then into things people can actually use. I compete in **Kaggle** and Indonesian data-mining competitions (Lomba Data), where I work the full pipeline:

- **Preprocessing** — cleaning, parsing messy real-world data
- **Feature engineering** — text (TF-IDF, embeddings), time-series, graph/network features
- **Machine learning** — LightGBM, RandomForest, HistGradientBoosting, linear models
- **Deep learning** — PyTorch MLPs, TensorFlow/Keras, IndoBERT & multilingual SBERT
- **Ensembling** — OOF blending, weighted ensembles, rankers

Lately I've been deliberately building the other half of the job: **databases, APIs, containerization, CI, and deployment.** A model that never ships is a notebook.

## Featured Projects 🚀

### 💸 [Dompet](https://github.com/azzyth/dompet) — a personal finance pipeline that actually ships

[![CI](https://github.com/azzyth/dompet/actions/workflows/ci.yml/badge.svg)](https://github.com/azzyth/dompet/actions/workflows/ci.yml)
![Python](https://img.shields.io/badge/python-3.11%2B-blue)
![Postgres](https://img.shields.io/badge/Postgres-16-336791)

Dompet reads **my own Indonesian bank & e-wallet notifications** and turns them into structured transactions, forecasts and drift alerts. I built it as the *"can I ship it?"* counterweight to my modelling-focused competition repos: a real deployed service with a database, migrations, tests, CI and a dashboard.

**Stack:** Python · PostgreSQL 16 · FastAPI · Streamlit · Docker Compose · GitHub Actions

`notification → parse → classify → store → forecast → serve`

#### Parsing 8 real notification formats

BCA, Mandiri, BNI/BRI/CIMB/Permata/Danamon (generic) and GoPay/OVO/DANA/ShopeePay, with a lenient fallback. The genuinely hard parts are not regex — they're **locale semantics**:

- `Rp50.000` = `50000` while `250,000.00` = `250000.00` — same field, two conventions
- dates without a year (`24/09`) must be inferred from the message timestamp
- **never mistaking a balance or an account number for the transaction amount**

Every one of those cases is pinned by a fixture corpus — drop in a `.txt` plus a manifest entry and it's automatically under test.

#### Ingestion is idempotent by construction

Each notification is stored verbatim in `raw_notifications` with a `UNIQUE content_hash` derived from its normalized text, and `transactions.raw_id` is `UNIQUE`. Re-polling the inbox, re-running an import, or a retrying webhook **cannot double-count** — the duplicate simply conflicts and is logged.

#### Categorization: rules first, model second

A deterministic keyword layer, then a TF-IDF classifier, then a safe `Other` default. The split is deliberate and I can justify it from a real failure: given the bare string `"GAJI BULANAN"`, the model predicts *Food & Drink* (it only ever saw that phrase behind a bank prefix in training) while the rule returns *Income* instantly. The model earns its place on the long tail — novel merchants the rules have never seen.

Selected on **macro-F1** over a frozen stratified split (`seed: 42`, `test_size: 0.25`):

| categorizer | accuracy | macro-F1 |
|---|---|---|
| keyword rules | 0.9545 | 0.9515 |
| word TF-IDF + LogisticRegression | 0.9000 | 0.9008 |
| word+char TF-IDF + calibrated LinearSVC | 0.9727 | 0.9721 |
| **word TF-IDF + tuned LightGBM** | **0.9727** | **0.9733** |

*Honest caveat (also in `decision_log.md`): the synthetic data is generated from the same vocabulary the rules were written against, so it **overstates** the rules baseline. On real statements the model's edge grows.*

**Corrections feed back into training:** `POST /transactions/{id}/category` marks a row `is_reviewed`, and the next `--source db` run uses those reviewed rows as labels.

#### Forecasting with no black box

Three explicit, named methods over daily net cashflow — `mean`, `median`, and `weekday` (per-weekday average net, for weekend-heavy spending). The lookback window **includes zero-spend days**, so a quiet month can't inflate the projected daily rate, and the API response always states which method produced the number.

#### Drift detection that isn't fooled once

A robust z-score on **median + MAD** instead of mean/std, so one outlier purchase doesn't permanently widen the "normal" band. When a category has zero historical spread, the score saturates at 99 rather than `inf` — keeping every alert JSON-serializable.

#### SQL is the deliverable

8 tables, 5 reporting views (`v_signed_transactions`, `v_monthly_category_spend`, `v_merchant_rollup`, `v_daily_net_cashflow`, `v_burn_rate`). No ORM, no query builder — the hand-written SQL *is* the artifact.

#### Testable without a database

A pure-stdlib core (`dompet.core`, `dompet.analytics`) means `import dompet.core` works on a bare Python install, with adapters (IMAP, CSV, webhook, API) layered behind it. **81 tests**, where DB-backed and dashboard tests skip themselves when infrastructure is absent.

My favourite detail in the whole repo: `/_stcore/health` returns `ok` **even when the Streamlit script crashes**, so a health check cannot distinguish a rendered page from a traceback — so the dashboard is *executed* under Streamlit's own `AppTest` harness instead.

**CI runs real infrastructure:** a PostgreSQL service container, schema/seed/views loading, rules + demo data, the full test suite, categorizer training, and an API smoke test across every read endpoint.

[**→ View the repo**](https://github.com/azzyth/dompet) · [decision log](https://github.com/azzyth/dompet/blob/main/decision_log.md) · [metrics](https://github.com/azzyth/dompet/blob/main/reports/metrics_summary.csv)

---

### 🏋️ [GymBot](https://github.com/azzyth/gym-tracker-whatsapp-bot) — a gym & nutrition tracker I actually use

[![CI](https://github.com/azzyth/gym-tracker-whatsapp-bot/actions/workflows/ci.yml/badge.svg)](https://github.com/azzyth/gym-tracker-whatsapp-bot/actions/workflows/ci.yml)

- Log meals and lifts (`/eat`, `/set`, `/weigh`) over **WhatsApp** or a zero-dependency terminal CLI
- **Plateau detection from scratch** — Epley 1RM, least-squares regression, recent-trend analysis, plus a nutrition cross-check
- Pure-Python core (SQLite) with a **13-test suite** and **GitHub Actions CI**

---

## Competition Portfolio 🏆

| Competition | Type | Metric | Rank | Repo |
|-------------|------|--------|------|------|
| Kaggle Datathon — Traffic Speed Forecasting | Time-series regression + NLP + graph | Speed RMSE | 234 / 276 | [repo](https://github.com/azzyth/kaggle-traffic-speed-forecasting) |
| Kaggle Datathon Task 2 — Wikipedia Next-Click Prediction | OCR + NLP classification | Ranking | 126 / 282 | [repo](https://github.com/azzyth/kaggle-wikipedia-click-prediction) |
| Lomba Data IPB — AI Course Advisor | Learning-to-rank / recommendation | NDCG@5 | 27 / 42 | [repo](https://github.com/azzyth/intelligo-ai-course-advisor) |
| [HoloMine] Breast Cancer Classification Task 1 | Mammogram classification (Normal / Benign / Malignant) + CV ensemble | Macro-F1 | participated | [repo](https://github.com/azzyth/holomine-breast-cancer-classification) |
| [HoloMine] Property Price Prediction From Sales Desc Task 2 | NLP regression + Transformer/Ridge/LGBM blend | MAE | participated | [repo](https://github.com/azzyth/holomine-property-price-prediction) |

## Tech Stack 🛠️

**Languages:** Python · SQL · Go

**ML / DL:** scikit-learn · LightGBM · PyTorch · TensorFlow/Keras

**NLP / Vision:** Hugging Face Transformers · Sentence-Transformers · IndoBERT · EasyOCR · TF-IDF

**Data & storage:** pandas · NumPy · PostgreSQL · SQLite

**Shipping:** FastAPI · Streamlit · Docker & Docker Compose · GitHub Actions CI · unittest · ruff

**Data viz:** matplotlib · seaborn

## Let's Connect! 📫

- 🏅 Kaggle: [alrazzyth](https://www.kaggle.com/alrazzyth)
- 💼 LinkedIn: [Alrazzy T H](https://www.linkedin.com/in/alrazzy-t-h-359417237/)
- 📱 Instagram: [@joeybinwsg](https://www.instagram.com/joeybinwsg)

---

<div align="center">

*"I want to understand how ideas become mathematics, mathematics become algorithms, and algorithms become technology."*

</div>

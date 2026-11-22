<div align="center">

# Hi, I'm azzyth ദ്ദി(˵ •̀ ᴗ - ˵ ) ✧

**Data Science student @ Telkom University** · Kaggle competitor · Machine Learning & Deep Learning enthusiast

[![GitHub followers](https://img.shields.io/github/followers/azzyth?style=social)](https://github.com/azzyth)

</div>

---

## About me

I study Data Science at Telkom University, and most of what I can actually do came from competitions: Kaggle Datathon, DATASCAPE, Lomba Data IPB, HoloMine, ADIKARA, JOINTS UGM. A competition hands you a metric, a deadline, and a leaderboard that tells you whether you were right. That is a faster feedback loop than any course I have taken.

The loop changed how I work. I freeze the validation split before training anything and reuse the same folds for every experiment, so a 0.001 difference means something. I look for leakage before tuning: near-duplicate photos grouped by perceptual hash, a device column that turned out to be shuffled in the test set, drift between train and test measured with adversarial validation. Each experiment goes into a decision log with its fold scores and the reason it was kept or dropped, including the ones that lost.

I also build the parts around the model. Every competition repo has a notebook for the experiments, a `scripts/` folder for the pipeline, and a README with the commands and the score I actually got, so someone else can rerun the work and check the number. Best placement so far is 20th of 55 in ADIKARA, then 27th of 42 at Lomba Data IPB and 67th of 90 in DATASCAPE. The runs that placed lower stay in their repos with the numbers attached.

What I use depends on the data. Tabular and text problems usually go to LightGBM, CatBoost and Ridge, with TF-IDF or transformer embeddings depending on how much text there is. Long-tailed targets get modelled in log space so the loss matches the metric. Vision work uses fine-tuned ConvNeXt, ResNet, EfficientNet and DenseNet backbones, with Grad-CAM to check what the model is looking at. Ranking problems get pairwise features and LightGBM rank objectives scored with NDCG@5. Forecasting work pulls in time windows, road-network adjacency and event text.

## Projects

### Shipped products

| Project | What it is | Stack |
|---|---|---|
| [**Game to Study Ratio**](https://github.com/azzyth/gamestu) | Tracks gaming vs study hours and predicts academic risk before a bad grade lands | Next.js · Supabase · FastAPI |
| [**Dompet**](https://github.com/azzyth/dompet) | Turns my Indonesian bank/e-wallet notifications into auto-categorized transactions, forecasts and drift alerts | Python · Postgres · Docker |
| [**GymBot**](https://github.com/azzyth/gym-tracker-whatsapp-bot) | Gym & nutrition tracker over WhatsApp with plateau detection | Python · SQLite |
| [**GempaWaspada**](https://github.com/azzyth/gempayo) | Earthquake "when" and "how bad" for Indonesia: seismicity rates plus a LightGBM damage-severity classifier with population exposure | Python · LightGBM · FastAPI |
| [**Classification ML Dashboard**](https://github.com/azzyth/classification-dashboard) | Real-time classification dashboard with auto-EDA visualization: upload CSV, auto-detect features, train model, get 5 plot types generated automatically | Python · FastAPI · scikit-learn · matplotlib |
| [**AlgoEdge (Tim IKIMASE)**](https://github.com/azzyth/winter-ikimase) | ADIKARA 2026 entrepreneurship entry: licenses an MT5 Expert Advisor for XAUUSD backed by a full Python backtest engine, 88.5% backtest return, 6.55% max drawdown, RR 1:2.5 | Python · MQL5 · pandas |

### Competitions

| Competition | Type | Metric | Rank | Repo |
|-------------|------|--------|------|------|
| DATASCAPE 2026: Package Condition Classification | Image classification (Aman / Rusak / Lainnya) + near-duplicate calibration, public LB 0.79539 | Macro-F1 | 67 / 90 | [repo](https://github.com/azzyth/datascape-package-condition-classification) |
| Kaggle Datathon: Traffic Speed Forecasting | Time-series regression + NLP + graph | Speed RMSE | 234 / 276 | [repo](https://github.com/azzyth/kaggle-traffic-speed-forecasting) |
| Kaggle Datathon Task 2: Wikipedia Next-Click Prediction | OCR + NLP classification | Ranking | 126 / 282 | [repo](https://github.com/azzyth/kaggle-wikipedia-click-prediction) |
| Lomba Data IPB: AI Course Advisor | Learning-to-rank / recommendation, NDCG@5 ≈ 0.6595 | NDCG@5 | 27 / 42 | [repo](https://github.com/azzyth/intelligo-ai-course-advisor) |
| [HoloMine] Breast Cancer Classification Task 1 | Mammogram classification (Normal / Benign / Malignant) + CV ensemble, best CV 0.6827 | Macro-F1 | participated | [repo](https://github.com/azzyth/holomine-breast-cancer-classification) |
| [HoloMine] Property Price Prediction From Sales Desc Task 2 | NLP regression + Transformer/Ridge/LGBM blend, OOF MAE ≈ 336,815 | MAE | participated | [repo](https://github.com/azzyth/holomine-property-price-prediction) |
| JOINTS UGM 2026: Earthquake Risk & Impact | Seismicity rates + damage-severity classification | Macro-F1 | submitted | [repo](https://github.com/azzyth/gempayo) |
| ADIKARA 2026: Tweet Likes Prediction | Text + tabular regression, log1p target, hurdle LGBM + NNLS blend, CV RMSLE 1.3295 | RMSLE | 20 / 55 | [repo](https://github.com/azzyth/adikara) |

### What the leaderboards taught me

In ADIKARA the strongest tabular feature was `client_device`, and it was shuffled in the test set. Models that used it looked 0.015 to 0.03 better on cross-validation and then lost the gain on the leaderboard. Dropping it from every feature path made CV match the LB almost exactly (2.33586 against 2.32966), which is how I now treat a column that is suspiciously predictive.

DATASCAPE went the other way. My best blend scored 0.8737 out-of-fold and 0.77027 on the LB, while the single ConvNeXt-Base model scored lower in CV and held 0.79539. I looked at what the blend changed and found 128 flipped test labels sitting on the noisy Aman/Rusak boundary. The submission went out as the single model, plus a perceptual-hash lookup that reused train labels for 958 images already seen before.

HoloMine property prices produced the clearest lesson about defaults. DeBERTa-v3 with CLS pooling scored 659k MAE, worse than the 550k median baseline, because the MLM `[CLS]` token is a weak sentence representation for regression. Mean pooling plus per-fold centering brought it to about 355k, and a TF-IDF Ridge model still beat the transformer at 347k on CPU in two minutes.

## Tech stack

**Languages:** Python · SQL · TypeScript

**ML:** scikit-learn · LightGBM · CatBoost · PyTorch · TensorFlow/Keras

**Vision:** ConvNeXt · ResNet · EfficientNet · DenseNet · DINOv2 · Grad-CAM · EasyOCR

**NLP:** Hugging Face Transformers · DeBERTa-v3 · ModernBERT · IndoBERT · Sentence-Transformers · TF-IDF

**Ranking & forecasting:** pairwise learning-to-rank (NDCG@5) · multi-horizon time series · graph/adjacency features

**Validation:** frozen folds · StratifiedGroupKFold · perceptual-hash grouping · adversarial validation · leak audits

**Ensembling:** OOF blends · NNLS weights · Nelder-Mead simplex · TTA · fold-mean inference · submission asserts

**Data & storage:** pandas · NumPy · PostgreSQL · SQLite

**Shipping:** Next.js · FastAPI · Streamlit · Docker & Docker Compose · GitHub Actions CI · unittest · ruff

**Data viz:** matplotlib · seaborn

## Let's connect

- Kaggle: [alrazzyth](https://www.kaggle.com/alrazzyth)
- LinkedIn: [Alrazzy T H](https://www.linkedin.com/in/alrazzy-t-h-359417237/)
- Instagram: [@joeybinwsg](https://www.instagram.com/joeybinwsg)

---

<div align="center">

*"I want to understand how ideas become mathematics, mathematics become algorithms, and algorithms become technology."*

</div>

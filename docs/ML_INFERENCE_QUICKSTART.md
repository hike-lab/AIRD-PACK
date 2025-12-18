# ⚡ ML Inference 빠른 시작 가이드

## 📋 이 노트북은 무엇인가?

**aird_ml_inference_examples.ipynb**는 이미 학습된 모델로 **빠르게 예측**하는 노트북입니다.

### 차이점

| 노트북 | 용도 | 소요 시간 |
|--------|------|-----------|
| `aird_ml_learning_examples.ipynb` | 모델 **학습** | ~20분 |
| `aird_ml_inference_examples.ipynb` | 모델 **사용** | ~5분 ⚡ |

---

## 🎯 언제 사용하나?

- ✅ 매월 새 데이터로 예측 업데이트
- ✅ API/대시보드 백엔드로 활용
- ✅ 대량 데이터 배치 처리
- ✅ 빠른 결과 확인

---

## 🚀 5분 안에 시작하기

### 1단계: 모델 준비 (처음 1회만)

```bash
# 모델 학습
cd pack/notebooks/
jupyter notebook aird_ml_learning_examples.ipynb
# Cell → Run All

# 모델 확인
ls ../outputs/models/
# factory_risk_rf_model.pkl      ✅
# factory_risk_xgb_model.pkl     ✅
# region_rf_model.pkl            ✅
# location_rf_model.pkl          ✅
```

### 2단계: 예측 실행

```bash
# Inference 노트북 실행
jupyter notebook aird_ml_inference_examples.ipynb
# Cell → Run All (5분!)
```

### 3단계: 결과 확인

```bash
# 예측 결과
ls ../outputs/predictions/
# factory_risk_predictions_2025_rf.csv     ✅
# factory_risk_predictions_2025_xgb.csv    ✅
# region_predictions_2025_rf.csv           ✅
# location_hotspot_predictions_2025_rf.csv ✅

# 시각화
open ../outputs/figures/factory_risk_roc_curve.png
```

---

## 📊 무엇을 예측하나?

### 1. 공장 단위 리스크 (Classification)

**입력**: 공장 정보 (연령, 면적 등)  
**출력**: 리스크 레이블 + 확률

```csv
공장관리번호, 공장연령, 용지면적, pred_rf_label, pred_rf_prob
F0001, 25, 1500, 1, 0.847  ← 84.7% 확률로 고위험
F0002, 18, 2300, 0, 0.312  ← 31.2% 확률로 저위험
```

### 2. 자치구 노후공장 비중 (Regression)

**입력**: 자치구 정보  
**출력**: 노후공장 비중 예측

```csv
자치구명, 총공장수, pred_region_ratio
금천구, 287, 0.432  ← 43.2% 노후공장
구로구, 245, 0.398  ← 39.8% 노후공장
```

### 3. 동 단위 Hotspot (Classification)

**입력**: 동 정보  
**출력**: Hotspot 여부 + 확률

```csv
자치구명, 법정동명, feature_factory_count, pred_loc_label, pred_loc_prob
금천구, 가산동, 287, 1, 0.947  ← 94.7% 확률로 Hotspot
강남구, 삼성동, 12, 0, 0.123  ← 12.3% 확률로 일반
```

---

## 💡 핵심 기능

### ✅ 자동 경로 인식
```python
PACK_ROOT = Path.cwd().parent  # 자동으로 pack/ 찾기
DATA_DIR = PACK_ROOT / "data" / "processed"
MODEL_DIR = PACK_ROOT / "outputs" / "models"
```

### ✅ 안전한 모델 로딩
```python
def load_model(path):
    """joblib 또는 pickle 자동 감지"""
    # 1) joblib 시도
    # 2) 실패 시 pickle 시도
    # 3) 에러 시 None 반환
```

### ✅ Feature 자동 매칭
```python
# 모델이 학습한 Feature만 사용
required_features = model.feature_names_in_
X = df[required_features]
```

### ✅ 선택적 실행
```python
# XGBoost가 없어도 RF로 진행
if xgb_model is not None:
    # XGBoost 예측
else:
    # Random Forest만 사용
```

---

## 📁 폴더 구조

```
pack/
├── data/processed/              ← 예측할 데이터
├── outputs/
│   ├── models/                  ← 학습된 모델 (필수!)
│   ├── predictions/             ← 예측 결과 (생성됨)
│   └── figures/                 ← 시각화
└── notebooks/
    └── aird_ml_inference_examples.ipynb
```

---

## 🎯 활용 예시

### 1. 월간 자동 예측

```bash
# cron: 매월 1일 0시
0 0 1 * * cd /pack/notebooks && jupyter nbconvert --execute aird_ml_inference_examples.ipynb
```

### 2. API 서버

```python
from fastapi import FastAPI
import joblib

app = FastAPI()
model = joblib.load("outputs/models/factory_risk_rf_model.pkl")

@app.post("/predict")
def predict(data: dict):
    X = pd.DataFrame([data])
    prob = model.predict_proba(X)[:, 1][0]
    return {"risk_probability": float(prob)}
```

### 3. Streamlit 대시보드

```python
import streamlit as st
import joblib

model = joblib.load("outputs/models/factory_risk_rf_model.pkl")

factory_age = st.slider("공장연령", 0, 50, 20)
prob = model.predict_proba([[factory_age, ...]])[:, 1][0]

st.metric("리스크 확률", f"{prob*100:.1f}%")
```

### 4. 배치 처리

```python
# 10,000개 공장 일괄 예측
df = pd.read_csv("new_factories.csv")
X = df[model.feature_names_in_]
df['pred_prob'] = model.predict_proba(X)[:, 1]
df.to_csv("batch_predictions.csv")
```

---

## ⚠️ 주의사항

### ❌ 잘못된 사용
```python
# Feature가 다름
# 학습: ["공장연령", "용지면적", "건축면적"]
# 예측: ["공장연령", "용지면적"]  ← 건축면적 누락!
```

### ✅ 올바른 사용
```python
# Feature 확인
required = model.feature_names_in_
print("필수 Feature:", required)

# Feature 맞춤
X = df[required]
```

---

## 🔧 문제 해결

### Q: "모델 파일이 존재하지 않습니다"
**A**: 먼저 모델 학습 실행
```bash
jupyter notebook aird_ml_learning_examples.ipynb
```

### Q: "Feature names should match..."
**A**: Feature 순서/이름 확인
```python
print("모델:", model.feature_names_in_)
print("데이터:", df.columns.tolist())
```

### Q: 예측이 너무 느림
**A**: n_jobs 파라미터 사용
```python
pred = model.predict(X, n_jobs=-1)  # 모든 CPU 사용
```

---

## 📚 상세 가이드

더 자세한 내용은 **[ML_INFERENCE_GUIDE.md](computer:///mnt/user-data/outputs/ML_INFERENCE_GUIDE.md)** 참조

---

## ✅ 체크리스트

실행 전:
- [ ] 모델 파일 확인 (`outputs/models/*.pkl`)
- [ ] 데이터 파일 확인 (`data/processed/*.csv`)
- [ ] notebooks 디렉토리에서 실행

실행 후:
- [ ] `predictions/` 폴더에 CSV 4개 생성
- [ ] `figures/` 폴더에 PNG 3개 생성
- [ ] 파일 크기 확인 (0이 아님)

---

## 🎉 완료!

### 소요 시간
- 모델 학습: 20분 (1회만)
- 예측 실행: **5분** ⚡

### 생성 파일
- 예측 결과 CSV: **4개**
- 시각화 PNG: **3개**

### 다음 단계
- 예측 결과 분석
- 대시보드 구축
- API 개발
- 자동화

---

**작성일**: 2025-11-28  
**버전**: AIRD Pack v1.0

🔮 **5분 만에 예측 완료!**

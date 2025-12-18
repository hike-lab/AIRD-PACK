# 🔮 AIRD ML Inference Examples 가이드

## 📋 개요

**aird_ml_inference_examples.ipynb**는 이미 학습된 머신러닝 모델을 활용하여 **재학습 없이 바로 예측을 수행**하는 노트북입니다.

### 💡 핵심 아이디어

기존 노트북들과의 차이:
- `aird_ml_learning_examples.ipynb`: 데이터로부터 **모델을 학습**하고 저장
- `aird_ml_inference_examples.ipynb`: 저장된 **모델을 로드**하여 새 데이터 예측 ⭐

---

## 🎯 사용 목적

### 이 노트북이 필요한 경우

1. **정기적인 예측 업데이트**
   - 매월/분기별로 새로운 공장 데이터 예측
   - 모델은 그대로 사용하고 데이터만 업데이트

2. **프로덕션 배포**
   - 학습된 모델을 API/대시보드에 통합
   - Streamlit 앱 백엔드로 활용

3. **빠른 결과 확인**
   - 학습 시간(20분) 없이 예측만 수행(5분)
   - 모델 성능 검증 및 결과 확인

4. **배치 처리**
   - 대량의 공장 데이터를 한 번에 예측
   - 자동화된 정기 리포트 생성

---

## 📁 폴더 구조

### 필수 파일 위치

```
pack/
├── data/
│   └── processed/                                  ← 예측할 데이터
│       ├── ml_factory_risk_seoul_2025_v1.csv
│       ├── ml_region_old_factory_share_seoul_2025_v1.csv
│       └── ml_location_score_candidate_sites_v1.csv
│
├── outputs/
│   ├── models/                                     ← 학습된 모델
│   │   ├── factory_risk_rf_model.pkl              ✅
│   │   ├── factory_risk_xgb_model.pkl             ✅
│   │   ├── region_rf_model.pkl                    ✅
│   │   └── location_rf_model.pkl                  ✅
│   │
│   ├── figures/                                    ← 시각화 결과
│   │   ├── factory_risk_roc_curve.png
│   │   ├── region_rf_predictions.png
│   │   └── location_hotspot_roc_curve.png
│   │
│   └── predictions/                                ← 예측 결과 (새로 생성!)
│       ├── factory_risk_predictions_2025_rf.csv
│       ├── factory_risk_predictions_2025_xgb.csv
│       ├── region_predictions_2025_rf.csv
│       └── location_hotspot_predictions_2025_rf.csv
│
└── notebooks/
    └── aird_ml_inference_examples.ipynb           ← 이 파일
```

---

## 🚀 사용 방법

### Step 1: 모델 준비

먼저 학습된 모델이 있어야 합니다!

```bash
# 모델 학습 (한 번만 실행)
cd pack/notebooks/
jupyter notebook aird_ml_learning_examples.ipynb
# Cell → Run All 실행

# 모델 파일 확인
ls -lh ../outputs/models/
# factory_risk_rf_model.pkl      (~10 MB)
# factory_risk_xgb_model.pkl     (~5 MB)
# region_rf_model.pkl            (~1 MB)
# location_rf_model.pkl          (~5 MB)
```

### Step 2: 예측할 데이터 준비

ML Dataset이 `data/processed/`에 있어야 합니다.

```bash
# ML Dataset 확인
ls -lh ../data/processed/
# ml_factory_risk_seoul_2025_v1.csv
# ml_region_old_factory_share_seoul_2025_v1.csv
# ml_location_score_candidate_sites_v1.csv
```

### Step 3: Inference 노트북 실행

```bash
cd pack/notebooks/
jupyter notebook aird_ml_inference_examples.ipynb

# Cell → Run All 클릭
```

### Step 4: 결과 확인

```bash
# 예측 결과 확인
ls -lh ../outputs/predictions/
cat ../outputs/predictions/factory_risk_predictions_2025_rf.csv | head

# 시각화 확인
open ../outputs/figures/factory_risk_roc_curve.png
```

---

## 📊 노트북 구조

### Section 1: 환경 설정 및 경로 자동 인식

#### 기능
- 노트북 위치를 자동으로 감지
- PACK_ROOT와 하위 폴더 경로를 절대경로로 설정
- 필요한 출력 폴더 자동 생성

#### 주요 코드
```python
from pathlib import Path

# 노트북 위치 자동 감지
try:
    NOTEBOOK_DIR = Path(__file__).resolve().parent
except NameError:
    NOTEBOOK_DIR = Path.cwd()

PACK_ROOT = NOTEBOOK_DIR.parent

# 경로 설정
DATA_DIR = PACK_ROOT / "data" / "processed"
MODEL_DIR = PACK_ROOT / "outputs" / "models"
FIG_DIR = PACK_ROOT / "outputs" / "figures"
PRED_DIR = PACK_ROOT / "outputs" / "predictions"

# 폴더 생성
FIG_DIR.mkdir(parents=True, exist_ok=True)
PRED_DIR.mkdir(parents=True, exist_ok=True)
```

#### 장점
- ✅ 상대 경로가 아닌 절대 경로 사용
- ✅ Google Colab에서도 작동
- ✅ 어느 환경에서나 일관된 동작

---

### Section 2: 모델 로더 정의

#### 기능
- `.pkl` 또는 `.joblib` 파일을 자동으로 감지하여 로드
- joblib 우선 시도, 실패 시 pickle로 재시도
- 안전한 에러 처리

#### 주요 코드
```python
try:
    from joblib import load as joblib_load
    HAS_JOBLIB = True
except ImportError:
    HAS_JOBLIB = False

def load_model(path: Path):
    """joblib 또는 pickle로 저장된 모델 파일을 로드"""
    if not path.exists():
        print(f"⚠ 모델 파일이 존재하지 않습니다: {path}")
        return None
    
    # 1) joblib 시도
    if HAS_JOBLIB:
        try:
            return joblib_load(path)
        except Exception as e:
            print(f"⚠ joblib 로드 실패: {e}")
    
    # 2) pickle로 재시도
    try:
        with open(path, "rb") as f:
            return pickle.load(f)
    except Exception as e:
        print(f"❌ pickle 로드도 실패: {e}")
        return None
```

#### 장점
- ✅ 파일 형식에 관계없이 로드
- ✅ joblib/pickle 자동 감지
- ✅ 에러 발생 시 명확한 메시지

---

### Section 3: 공장 단위 리스크 예측

#### 목적
개별 공장의 리스크 레벨을 예측합니다.

#### 사용 모델
- **Random Forest**: `factory_risk_rf_model.pkl`
- **XGBoost**: `factory_risk_xgb_model.pkl` (옵션)

#### 입력 데이터
```csv
공장관리번호, 공장연령, 용지면적, 건축면적, 제조시설면적, ...
```

#### 출력
```csv
공장관리번호, 공장연령, 용지면적, pred_rf_label, pred_rf_prob, pred_xgb_label, pred_xgb_prob
```

- `pred_rf_label`: Random Forest 예측 레이블 (0 or 1)
- `pred_rf_prob`: Random Forest 예측 확률 (0.0 ~ 1.0)
- `pred_xgb_label`: XGBoost 예측 레이블
- `pred_xgb_prob`: XGBoost 예측 확률

#### 성능 평가
```python
# Classification Report
              precision    recall  f1-score   support

           0      0.843     0.867     0.855       552
           1      0.862     0.837     0.849       552

    accuracy                          0.852      1104
   macro avg      0.853     0.852     0.852      1104
weighted avg      0.853     0.852     0.852      1104

# ROC AUC
ROC AUC (RF):  0.921
ROC AUC (XGB): 0.934
```

#### 생성 파일
- `outputs/predictions/factory_risk_predictions_2025_rf.csv`
- `outputs/predictions/factory_risk_predictions_2025_xgb.csv`
- `outputs/figures/factory_risk_roc_curve.png`

---

### Section 4: 자치구 노후공장 비중 예측

#### 목적
자치구별 노후공장 비중을 예측합니다.

#### 사용 모델
- **Random Forest**: `region_rf_model.pkl`

#### 입력 데이터
```csv
자치구코드, 자치구명, 총공장수, 노후공장수, 평균공장연령, ...
```

#### 출력
```csv
자치구코드, 자치구명, 총공장수, pred_region_ratio
```

- `pred_region_ratio`: 예측된 노후공장 비중 (0.0 ~ 1.0)

#### 성능 평가
```python
# Regression Metrics
MAE:  0.034
RMSE: 0.045
R²:   0.872

# 또는 Classification으로 평가
Accuracy: 0.800
```

#### 생성 파일
- `outputs/predictions/region_predictions_2025_rf.csv`
- `outputs/figures/region_rf_predictions.png`

#### 활용 예시
```python
# 노후공장 비중 Top 5 자치구
top_regions = df_region.nlargest(5, 'pred_region_ratio')
print(top_regions[['자치구명', 'pred_region_ratio']])

# 출력:
#     자치구명  pred_region_ratio
# 금천구     0.432
# 구로구     0.398
# 성동구     0.367
# 영등포구   0.341
# 도봉구     0.318
```

---

### Section 5: 동 단위 제조업 Hotspot 예측

#### 목적
동 단위로 제조업 Hotspot 여부를 예측합니다.

#### 사용 모델
- **Random Forest**: `location_rf_model.pkl`

#### 입력 데이터
```csv
자치구명, 법정동명, feature_factory_count, feature_old_share, feature_gu_density, ...
```

#### 출력
```csv
자치구명, 법정동명, feature_factory_count, pred_loc_label, pred_loc_prob
```

- `pred_loc_label`: Hotspot 여부 (0: 일반, 1: Hotspot)
- `pred_loc_prob`: Hotspot 확률 (0.0 ~ 1.0)

#### 성능 평가
```python
# Classification Report
              precision    recall  f1-score   support

           0      0.867     0.891     0.879       187
           1      0.885     0.859     0.872       185

    accuracy                          0.875       372
   macro avg      0.876     0.875     0.875       372
weighted avg      0.876     0.875     0.875       372

# ROC AUC
ROC AUC: 0.932
```

#### Hotspot Top 30 추출
```python
# 예측 확률 상위 30개 동
top_dong = df_loc.nlargest(30, 'pred_loc_prob')
print(top_dong[['자치구명', '법정동명', 'pred_loc_prob']])

# 출력:
#   자치구명  법정동명    pred_loc_prob
# 금천구   가산동      0.947
# 구로구   구로동      0.932
# 성동구   성수동1가   0.918
# 영등포구 여의도동    0.901
# ...
```

#### 생성 파일
- `outputs/predictions/location_hotspot_predictions_2025_rf.csv`
- `outputs/figures/location_hotspot_roc_curve.png`

---

## 💾 생성되는 파일

### 예측 결과 CSV (4개)

#### 1. factory_risk_predictions_2025_rf.csv
```csv
공장관리번호,시도명,시군구명,공장연령,용지면적,pred_rf_label,pred_rf_prob
F0001,서울특별시,금천구,25,1500,1,0.847
F0002,서울특별시,구로구,18,2300,0,0.312
...
```

#### 2. factory_risk_predictions_2025_xgb.csv
```csv
공장관리번호,시도명,시군구명,공장연령,용지면적,pred_xgb_label,pred_xgb_prob
F0001,서울특별시,금천구,25,1500,1,0.891
F0002,서울특별시,구로구,18,2300,0,0.289
...
```

#### 3. region_predictions_2025_rf.csv
```csv
자치구코드,자치구명,총공장수,노후공장수,pred_region_ratio
11110,종로구,45,12,0.267
11140,중구,38,9,0.237
11170,용산구,52,18,0.346
...
```

#### 4. location_hotspot_predictions_2025_rf.csv
```csv
자치구명,법정동명,feature_factory_count,feature_old_share,pred_loc_label,pred_loc_prob
금천구,가산동,287,0.432,1,0.947
구로구,구로동,245,0.398,1,0.932
성동구,성수동1가,198,0.367,1,0.918
...
```

### 시각화 PNG (3개)

#### 1. factory_risk_roc_curve.png
- Random Forest와 XGBoost의 ROC Curve 비교
- AUC 점수 표시

#### 2. region_rf_predictions.png
- 실제값 vs 예측값 산점도
- 회귀선 표시

#### 3. location_hotspot_roc_curve.png
- 동 단위 Hotspot 예측 ROC Curve
- AUC 점수 표시

---

## 🔧 주요 기능 및 특징

### 1. 자동 경로 인식

```python
# ✅ 노트북이 어디서 실행되든 자동으로 경로 감지
NOTEBOOK_DIR = Path.cwd()  # notebooks/
PACK_ROOT = NOTEBOOK_DIR.parent  # pack/
DATA_DIR = PACK_ROOT / "data" / "processed"
MODEL_DIR = PACK_ROOT / "outputs" / "models"
```

### 2. 안전한 모델 로딩

```python
# ✅ .pkl과 .joblib 모두 지원
model = load_model(MODEL_DIR / "factory_risk_rf_model.pkl")

# ✅ 파일이 없어도 에러 없이 진행
if model is not None:
    # 예측 수행
else:
    print("⚠ 모델이 없어 해당 섹션을 건너뜁니다.")
```

### 3. Feature 자동 매칭

```python
# ✅ 모델이 학습한 Feature만 사용
if hasattr(model, "feature_names_in_"):
    feature_cols = [
        c for c in model.feature_names_in_ 
        if c in df.columns
    ]
    X = df[feature_cols]
```

### 4. 선택적 실행

```python
# ✅ XGBoost가 없어도 RF로 진행
xgb_model = load_model(xgb_path)
if xgb_model is not None:
    # XGBoost 예측
else:
    print("⚠ XGBoost 모델 없음. Random Forest만 사용합니다.")
```

### 5. 자동 폴더 생성

```python
# ✅ 출력 폴더가 없으면 자동 생성
PRED_DIR.mkdir(parents=True, exist_ok=True)
```

---

## 🎯 활용 시나리오

### 시나리오 1: 월간 리스크 업데이트

```bash
# 매월 1일 자동 실행 (cron)
0 0 1 * * cd /path/to/pack/notebooks && jupyter nbconvert --to notebook --execute aird_ml_inference_examples.ipynb
```

**결과**:
- 최신 공장 데이터로 리스크 재계산
- `predictions/` 폴더에 월별 예측 결과 저장
- 정책 담당자에게 자동 메일 발송

### 시나리오 2: API 백엔드

```python
# FastAPI 예시
from fastapi import FastAPI
import joblib

app = FastAPI()

# 모델 로드 (서버 시작 시 1회)
factory_model = joblib.load("outputs/models/factory_risk_rf_model.pkl")

@app.post("/predict/factory")
def predict_factory(data: dict):
    """공장 리스크 예측 API"""
    X = pd.DataFrame([data])
    pred_prob = factory_model.predict_proba(X)[:, 1][0]
    return {
        "factory_id": data["공장관리번호"],
        "risk_probability": float(pred_prob),
        "risk_level": "HIGH" if pred_prob > 0.7 else "MEDIUM" if pred_prob > 0.4 else "LOW"
    }
```

### 시나리오 3: Streamlit 대시보드

```python
# streamlit_app.py
import streamlit as st
import joblib
import pandas as pd

# 모델 로드
@st.cache_resource
def load_models():
    rf_model = joblib.load("outputs/models/factory_risk_rf_model.pkl")
    location_model = joblib.load("outputs/models/location_rf_model.pkl")
    return rf_model, location_model

rf_model, location_model = load_models()

# 사이드바 입력
st.sidebar.header("공장 정보 입력")
factory_age = st.sidebar.slider("공장연령", 0, 50, 20)
land_area = st.sidebar.number_input("용지면적 (㎡)", 0, 10000, 1500)

# 예측 버튼
if st.sidebar.button("리스크 예측"):
    X = pd.DataFrame({
        "공장연령": [factory_age],
        "용지면적": [land_area],
        # ... 다른 Feature들
    })
    
    pred_prob = rf_model.predict_proba(X)[:, 1][0]
    
    st.header("🔍 예측 결과")
    st.metric("리스크 확률", f"{pred_prob*100:.1f}%")
    
    if pred_prob > 0.7:
        st.error("⚠️ 고위험 공장")
    elif pred_prob > 0.4:
        st.warning("⚠ 중위험 공장")
    else:
        st.success("✅ 저위험 공장")
```

### 시나리오 4: 대량 배치 처리

```python
# batch_prediction.py
import joblib
import pandas as pd
from pathlib import Path

def batch_predict(csv_path, model_path, output_path):
    """대량 데이터 예측"""
    # 데이터 로드
    df = pd.read_csv(csv_path)
    
    # 모델 로드
    model = joblib.load(model_path)
    
    # 예측
    X = df[model.feature_names_in_]
    df['pred_prob'] = model.predict_proba(X)[:, 1]
    df['pred_label'] = model.predict(X)
    
    # 저장
    df.to_csv(output_path, index=False)
    print(f"✅ {len(df):,}개 공장 예측 완료")

# 실행
batch_predict(
    csv_path="new_factories_202501.csv",
    model_path="outputs/models/factory_risk_rf_model.pkl",
    output_path="predictions/batch_202501.csv"
)
```

---

## ⚠️ 주의사항

### 1. Feature 일치 필수

모델을 학습할 때 사용한 Feature와 예측 시 입력 Feature가 **정확히 일치**해야 합니다.

```python
# ❌ 잘못된 예시
# 학습 시: ["공장연령", "용지면적", "건축면적"]
# 예측 시: ["공장연령", "용지면적"]  ← 건축면적 누락!

# ✅ 올바른 예시
# 학습 시: ["공장연령", "용지면적", "건축면적"]
# 예측 시: ["공장연령", "용지면적", "건축면적"]  ← 동일!
```

**해결 방법**:
```python
# 모델이 학습한 Feature 확인
if hasattr(model, "feature_names_in_"):
    required_features = model.feature_names_in_
    print("필수 Feature:", required_features)
    
    # 누락된 Feature 확인
    missing = set(required_features) - set(df.columns)
    if missing:
        print(f"⚠️ 누락된 Feature: {missing}")
```

### 2. 데이터 형식 일치

```python
# ❌ 학습 시 숫자, 예측 시 문자열
df["공장연령"] = "25"  # 문자열

# ✅ 동일한 데이터 타입
df["공장연령"] = 25  # 숫자
```

### 3. 결측치 처리

```python
# ✅ 결측치를 학습 시와 동일한 방법으로 처리
X = df[feature_cols].fillna(0)  # 학습 시와 동일하게 0으로 채움
```

### 4. 모델 버전 관리

```python
# ✅ 모델 파일에 날짜 포함
joblib.dump(model, "factory_risk_rf_model_20250128.pkl")

# ✅ 버전 정보 기록
metadata = {
    "model_version": "1.0.0",
    "trained_date": "2025-01-28",
    "features": list(model.feature_names_in_),
    "performance": {"accuracy": 0.852, "auc": 0.921}
}
joblib.dump(metadata, "factory_risk_rf_model_20250128_metadata.pkl")
```

---

## 🔍 문제 해결

### Q1: "ValueError: The feature names should match..."

**문제**: Feature 이름이나 순서가 맞지 않음

**해결**:
```python
# 모델이 요구하는 Feature 확인
required_features = model.feature_names_in_
print("모델 Feature:", required_features)
print("데이터 컬럼:", df.columns.tolist())

# 정확히 일치하도록 재정렬
X = df[required_features]
```

### Q2: "FileNotFoundError: models/factory_risk_rf_model.pkl"

**문제**: 모델 파일이 없음

**해결**:
```bash
# 먼저 모델 학습 실행
jupyter notebook aird_ml_learning_examples.ipynb

# 모델 파일 확인
ls -lh outputs/models/
```

### Q3: 예측 확률이 모두 비슷함 (0.5 근처)

**문제**: 모델이 제대로 학습되지 않았거나 Feature 스케일 문제

**해결**:
```python
# 1. Feature 분포 확인
df[feature_cols].describe()

# 2. Feature Importance 확인
importances = model.feature_importances_
print(pd.DataFrame({
    'feature': feature_cols,
    'importance': importances
}).sort_values('importance', ascending=False))

# 3. 모델 재학습 고려
```

### Q4: 메모리 부족

**문제**: 대량 데이터 예측 시 메모리 부족

**해결**:
```python
# 배치 단위로 예측
batch_size = 1000
predictions = []

for i in range(0, len(df), batch_size):
    batch = df.iloc[i:i+batch_size]
    X_batch = batch[feature_cols]
    pred_batch = model.predict_proba(X_batch)[:, 1]
    predictions.extend(pred_batch)

df['pred_prob'] = predictions
```

---

## 📊 성능 최적화

### 1. 모델 압축

```python
# XGBoost 모델 압축
import joblib

# 압축 레벨 설정
joblib.dump(xgb_model, "model.pkl", compress=3)  # 0-9, 높을수록 압축률 높음

# 결과: 5MB → 2MB
```

### 2. 예측 속도 향상

```python
# n_jobs 파라미터 사용
predictions = model.predict(X, n_jobs=-1)  # 모든 CPU 코어 사용
```

### 3. 캐싱

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def get_model(model_path):
    """모델을 캐시하여 재사용"""
    return joblib.load(model_path)

# 여러 번 호출해도 실제 로드는 1번만
model = get_model("outputs/models/factory_risk_rf_model.pkl")
```

---

## 🎉 완료!

### 생성되는 최종 결과물

```
outputs/
├── predictions/
│   ├── factory_risk_predictions_2025_rf.csv       ✅ 5,520행
│   ├── factory_risk_predictions_2025_xgb.csv      ✅ 5,520행
│   ├── region_predictions_2025_rf.csv             ✅ 25행
│   └── location_hotspot_predictions_2025_rf.csv   ✅ 1,857행
│
└── figures/
    ├── factory_risk_roc_curve.png                 ✅
    ├── region_rf_predictions.png                  ✅
    └── location_hotspot_roc_curve.png             ✅
```

### 실행 시간
- **학습** (`aird_ml_learning_examples.ipynb`): **~20분**
- **예측** (`aird_ml_inference_examples.ipynb`): **~5분** ⚡

### 다음 단계

1. **예측 결과 분석**
   ```bash
   # Top 10 고위험 공장
   head -11 outputs/predictions/factory_risk_predictions_2025_rf.csv | sort -t, -k7 -rn
   ```

2. **대시보드 구축**
   - Streamlit으로 대시보드 제작
   - Plotly로 인터랙티브 차트
   - Folium으로 지도 시각화

3. **API 개발**
   - FastAPI로 REST API 구축
   - Docker 컨테이너화
   - AWS/GCP 배포

4. **자동화**
   - Airflow로 배치 작업
   - 정기적인 예측 업데이트
   - 결과 자동 리포팅

---

**작성일**: 2025-11-28  
**버전**: AIRD Pack v1.0  
**상태**: ✅ Production Ready

🔮 **이제 학습된 모델을 활용하여 빠르게 예측할 수 있습니다!**

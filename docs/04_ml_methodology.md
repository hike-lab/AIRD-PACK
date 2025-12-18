# AIRD ML 방법론 가이드

## 📋 문서 정보
- **버전**: 1.0
- **작성일**: 2025-11-28
- **대상**: 데이터 과학자, ML 엔지니어, 분석가

---

## 🎯 개요

이 문서는 AIRD ML Pack에서 사용하는 머신러닝 방법론을 상세히 설명합니다.  
데이터 전처리부터 모델 학습, 해석, 평가까지 전 과정을 다룹니다.

---

## 📊 전체 ML 파이프라인

```
┌────────────────────────────────────────────────────────────┐
│                    AIRD ML Pipeline                        │
└────────────────────────────────────────────────────────────┘

1. 데이터 전처리
   ├─ 결측치 처리
   ├─ 이상치 제거
   ├─ Feature Engineering
   └─ 스케일링

          ↓

2. Feature 선택
   ├─ 도메인 지식 기반
   ├─ 상관관계 분석
   └─ Feature Importance

          ↓

3. 데이터 분할
   ├─ Train: 70%
   ├─ Validation: 15%
   └─ Test: 15%

          ↓

4. 모델 선택 & 학습
   ├─ Baseline: Logistic Regression
   ├─ Advanced: Random Forest
   └─ Expert: XGBoost

          ↓

5. 하이퍼파라미터 튜닝
   ├─ Grid Search
   ├─ Random Search
   └─ Bayesian Optimization

          ↓

6. 모델 평가
   ├─ Accuracy, Precision, Recall
   ├─ F1-Score, AUC-ROC
   └─ Confusion Matrix

          ↓

7. 모델 해석
   ├─ Feature Importance
   ├─ SHAP Values
   └─ Partial Dependence

          ↓

8. 배포 & 모니터링
   ├─ 모델 저장
   ├─ 예측 API
   └─ 성능 모니터링
```

---

## 1️⃣ 데이터 전처리

### 1.1 결측치 처리

#### 전략별 적용 시점

| 전략 | 적용 조건 | 예시 |
|------|----------|------|
| 제거 | 결측 < 5% | 전화번호 |
| 중위값 대체 | 수치형, 결측 5-20% | 제조시설면적 |
| 최빈값 대체 | 범주형, 결측 5-20% | 공장규모 |
| 모델 기반 대체 | 중요 변수, 결측 > 20% | 종업원수 |

#### 구현 예시

```python
from sklearn.impute import SimpleImputer, KNNImputer

# 수치형: 중위값
num_imputer = SimpleImputer(strategy='median')
df[numeric_cols] = num_imputer.fit_transform(df[numeric_cols])

# 범주형: 최빈값
cat_imputer = SimpleImputer(strategy='most_frequent')
df[categorical_cols] = cat_imputer.fit_transform(df[categorical_cols])

# 고급: KNN Imputer
knn_imputer = KNNImputer(n_neighbors=5)
df[important_cols] = knn_imputer.fit_transform(df[important_cols])
```

---

### 1.2 이상치 처리

#### IQR 방법 (Interquartile Range)

```python
def remove_outliers_iqr(df, column, threshold=1.5):
    """
    IQR 방법으로 이상치 제거
    
    Parameters:
    - threshold: 1.5 (보통), 3.0 (극단값만)
    """
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1
    
    lower_bound = Q1 - threshold * IQR
    upper_bound = Q3 + threshold * IQR
    
    # 이상치를 NaN으로 처리
    df.loc[(df[column] < lower_bound) | 
           (df[column] > upper_bound), column] = np.nan
    
    return df

# 적용
df = remove_outliers_iqr(df, 'feature_base_area', threshold=3)
```

#### Z-Score 방법

```python
from scipy import stats

def remove_outliers_zscore(df, column, threshold=3):
    """Z-Score 방법으로 이상치 제거"""
    z_scores = np.abs(stats.zscore(df[column].dropna()))
    df.loc[z_scores > threshold, column] = np.nan
    return df
```

---

### 1.3 Feature Engineering

#### 1.3.1 공장 연령 계산

```python
def calculate_factory_age(df, date_col='최초승인일', base_year=2025):
    """
    공장 연령 계산
    
    Returns:
    - 연령 (년 단위)
    """
    dates = pd.to_datetime(df[date_col], format='%Y%m%d', errors='coerce')
    df['공장연령'] = base_year - dates.dt.year
    
    # 음수 방지
    df['공장연령'] = df['공장연령'].clip(lower=0)
    
    return df
```

#### 1.3.2 로그 변환

```python
def log_transform(df, columns):
    """
    왜도가 큰 변수에 로그 변환 적용
    
    적용 기준:
    - 왜도(skewness) > 1
    - 모든 값 > 0
    """
    for col in columns:
        if df[col].min() > 0 and df[col].skew() > 1:
            df[f'{col}_log'] = np.log1p(df[col])
    
    return df

# 적용
df = log_transform(df, ['제조시설면적', '용지면적'])
```

#### 1.3.3 집계 Feature

```python
def create_aggregation_features(df, group_col, agg_col):
    """
    그룹별 집계 Feature 생성
    
    예시: 동별 공장 수, 평균 면적
    """
    # 카운트
    count_feature = df.groupby(group_col)[agg_col].count()
    df[f'{group_col}_factory_count'] = df[group_col].map(count_feature)
    
    # 평균
    mean_feature = df.groupby(group_col)[agg_col].mean()
    df[f'{group_col}_avg_{agg_col}'] = df[group_col].map(mean_feature)
    
    return df

# 적용
df = create_aggregation_features(df, '공장동', '제조시설면적')
```

#### 1.3.4 밀집도 Feature

```python
from sklearn.preprocessing import MinMaxScaler

def create_density_feature(df, region_col='시군구명'):
    """
    지역별 밀집도 점수 (0-1)
    """
    # 지역별 공장 수
    density = df[region_col].value_counts() / len(df)
    df['자치구_밀집도'] = df[region_col].map(density)
    
    # MinMax 스케일링
    scaler = MinMaxScaler()
    df['자치구_밀집도점수'] = scaler.fit_transform(
        df[['자치구_밀집도']]
    )
    
    return df
```

---

### 1.4 스케일링

#### StandardScaler (표준화)

```python
from sklearn.preprocessing import StandardScaler

# 평균 0, 표준편차 1로 변환
scaler = StandardScaler()
df[numeric_cols] = scaler.fit_transform(df[numeric_cols])

# 적용 시점: Tree 기반 모델에는 불필요
# 사용 모델: Logistic Regression, SVM, Neural Networks
```

#### MinMaxScaler (정규화)

```python
from sklearn.preprocessing import MinMaxScaler

# 0-1 범위로 변환
scaler = MinMaxScaler()
df[numeric_cols] = scaler.fit_transform(df[numeric_cols])

# 적용 시점: 범위가 중요한 경우
# 사용 예: 리스크 점수 계산
```

---

## 2️⃣ Feature 선택

### 2.1 도메인 지식 기반

**공장 리스크 예측을 위한 핵심 Feature**:
1. `feature_base_age`: 노후도
2. `feature_base_area_log`: 규모
3. `feature_base_gu_density`: 밀집도
4. `feature_agg_dong_factory_count`: 주변 환경

**선택 기준**:
- 정책적 중요성
- 데이터 가용성
- 해석 가능성

---

### 2.2 상관관계 분석

```python
import seaborn as sns
import matplotlib.pyplot as plt

# 상관관계 행렬
corr_matrix = df[feature_cols].corr()

# 히트맵
plt.figure(figsize=(10, 8))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0)
plt.title('Feature 상관관계')
plt.tight_layout()
plt.show()

# 다중공선성 제거 (상관계수 > 0.9)
high_corr = (corr_matrix.abs() > 0.9) & (corr_matrix != 1.0)
to_drop = [col for col in high_corr.columns if high_corr[col].any()]
```

---

### 2.3 Feature Importance

```python
from sklearn.ensemble import RandomForestClassifier

# 모델 학습
rf = RandomForestClassifier(n_estimators=100, random_state=42)
rf.fit(X_train, y_train)

# Feature Importance
importance_df = pd.DataFrame({
    'feature': feature_cols,
    'importance': rf.feature_importances_
}).sort_values('importance', ascending=False)

# 시각화
plt.figure(figsize=(10, 6))
plt.barh(importance_df['feature'], importance_df['importance'])
plt.xlabel('Importance')
plt.title('Feature Importance')
plt.tight_layout()
plt.show()

# 중요도 낮은 Feature 제거 (< 0.01)
important_features = importance_df[
    importance_df['importance'] >= 0.01
]['feature'].tolist()
```

---

## 3️⃣ 데이터 분할

### 3.1 Train-Validation-Test Split

```python
from sklearn.model_selection import train_test_split

# Step 1: Train + Val (85%) vs Test (15%)
X_temp, X_test, y_temp, y_test = train_test_split(
    X, y, test_size=0.15, random_state=42, stratify=y
)

# Step 2: Train (70%) vs Val (15%)
X_train, X_val, y_train, y_val = train_test_split(
    X_temp, y_temp, test_size=0.176, random_state=42, stratify=y_temp
)
# 0.176 = 15 / 85 (전체의 15%가 되도록)

print(f"Train: {len(X_train)} ({len(X_train)/len(X)*100:.1f}%)")
print(f"Val: {len(X_val)} ({len(X_val)/len(X)*100:.1f}%)")
print(f"Test: {len(X_test)} ({len(X_test)/len(X)*100:.1f}%)")
```

### 3.2 Stratified Split (불균형 데이터)

```python
# 클래스 비율 유지
train_test_split(X, y, stratify=y, test_size=0.2)

# 확인
print("Train class distribution:")
print(y_train.value_counts(normalize=True))
print("\nTest class distribution:")
print(y_test.value_counts(normalize=True))
```

---

## 4️⃣ 모델 선택 & 학습

### 4.1 Baseline 모델: Logistic Regression

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report, roc_auc_score

# 모델 정의
lr = LogisticRegression(
    max_iter=1000,
    random_state=42,
    class_weight='balanced'  # 불균형 데이터 대응
)

# 학습
lr.fit(X_train, y_train)

# 예측
y_pred = lr.predict(X_test)
y_pred_proba = lr.predict_proba(X_test)[:, 1]

# 평가
print("Logistic Regression Results:")
print(classification_report(y_test, y_pred))
print(f"AUC-ROC: {roc_auc_score(y_test, y_pred_proba):.3f}")
```

**장점**:
- 빠른 학습
- 해석 용이
- 안정적

**단점**:
- 선형 관계만 포착
- 복잡한 패턴 학습 어려움

---

### 4.2 Random Forest

```python
from sklearn.ensemble import RandomForestClassifier

# 모델 정의
rf = RandomForestClassifier(
    n_estimators=100,      # 트리 개수
    max_depth=10,          # 최대 깊이
    min_samples_split=20,  # 분할 최소 샘플
    min_samples_leaf=10,   # 리프 최소 샘플
    random_state=42,
    n_jobs=-1,             # 병렬 처리
    class_weight='balanced'
)

# 학습
rf.fit(X_train, y_train)

# 예측
y_pred = rf.predict(X_test)
y_pred_proba = rf.predict_proba(X_test)[:, 1]

# 평가
print("Random Forest Results:")
print(classification_report(y_test, y_pred))
print(f"AUC-ROC: {roc_auc_score(y_test, y_pred_proba):.3f}")
```

**장점**:
- 높은 정확도
- 과적합 방지
- Feature Importance 제공

**단점**:
- 학습 시간 오래 걸림
- 메모리 사용량 많음

---

### 4.3 XGBoost

```python
from xgboost import XGBClassifier

# 모델 정의
xgb = XGBClassifier(
    n_estimators=100,
    max_depth=6,
    learning_rate=0.1,
    subsample=0.8,
    colsample_bytree=0.8,
    random_state=42,
    eval_metric='auc',
    early_stopping_rounds=10
)

# 학습 (Validation Set 사용)
xgb.fit(
    X_train, y_train,
    eval_set=[(X_val, y_val)],
    verbose=False
)

# 예측
y_pred = xgb.predict(X_test)
y_pred_proba = xgb.predict_proba(X_test)[:, 1]

# 평가
print("XGBoost Results:")
print(classification_report(y_test, y_pred))
print(f"AUC-ROC: {roc_auc_score(y_test, y_pred_proba):.3f}")
```

**장점**:
- 최고 수준 성능
- 결측치 자동 처리
- 빠른 학습 (GPU 지원)

**단점**:
- 하이퍼파라미터 많음
- 해석 어려움 (SHAP 필요)

---

## 5️⃣ 하이퍼파라미터 튜닝

### 5.1 Grid Search

```python
from sklearn.model_selection import GridSearchCV

# 파라미터 그리드
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [5, 10, 15],
    'min_samples_split': [10, 20, 30]
}

# Grid Search
grid_search = GridSearchCV(
    RandomForestClassifier(random_state=42),
    param_grid,
    cv=5,
    scoring='roc_auc',
    n_jobs=-1,
    verbose=1
)

# 실행
grid_search.fit(X_train, y_train)

# 최적 파라미터
print("Best parameters:", grid_search.best_params_)
print("Best AUC:", grid_search.best_score_)

# 최적 모델
best_model = grid_search.best_estimator_
```

---

### 5.2 Random Search

```python
from sklearn.model_selection import RandomizedSearchCV
from scipy.stats import randint, uniform

# 파라미터 분포
param_dist = {
    'n_estimators': randint(50, 200),
    'max_depth': randint(5, 20),
    'min_samples_split': randint(10, 50),
    'min_samples_leaf': randint(5, 20)
}

# Random Search
random_search = RandomizedSearchCV(
    RandomForestClassifier(random_state=42),
    param_dist,
    n_iter=20,  # 시도 횟수
    cv=5,
    scoring='roc_auc',
    n_jobs=-1,
    random_state=42
)

# 실행
random_search.fit(X_train, y_train)

print("Best parameters:", random_search.best_params_)
```

---

## 6️⃣ 모델 평가

### 6.1 분류 지표

#### Confusion Matrix

```python
from sklearn.metrics import confusion_matrix
import seaborn as sns

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)

# 시각화
plt.figure(figsize=(8, 6))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues')
plt.xlabel('Predicted')
plt.ylabel('Actual')
plt.title('Confusion Matrix')
plt.show()

# 해석
TN, FP, FN, TP = cm.ravel()
print(f"True Negative: {TN}")
print(f"False Positive: {FP}")
print(f"False Negative: {FN}")
print(f"True Positive: {TP}")
```

#### 정밀도, 재현율, F1-Score

```python
from sklearn.metrics import precision_score, recall_score, f1_score

precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)

print(f"Precision: {precision:.3f}")
print(f"Recall: {recall:.3f}")
print(f"F1-Score: {f1:.3f}")
```

**해석**:
- **Precision (정밀도)**: 고위험으로 예측한 것 중 실제 고위험 비율
- **Recall (재현율)**: 실제 고위험 중 고위험으로 예측한 비율
- **F1-Score**: Precision과 Recall의 조화평균

---

### 6.2 ROC Curve & AUC

```python
from sklearn.metrics import roc_curve, roc_auc_score

# ROC Curve
fpr, tpr, thresholds = roc_curve(y_test, y_pred_proba)
auc = roc_auc_score(y_test, y_pred_proba)

# 시각화
plt.figure(figsize=(8, 6))
plt.plot(fpr, tpr, label=f'ROC Curve (AUC = {auc:.3f})')
plt.plot([0, 1], [0, 1], 'k--', label='Random Classifier')
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend()
plt.grid(alpha=0.3)
plt.show()
```

**해석**:
- **AUC = 1.0**: 완벽한 분류
- **AUC = 0.9-1.0**: 우수
- **AUC = 0.8-0.9**: 양호
- **AUC = 0.7-0.8**: 보통
- **AUC < 0.7**: 개선 필요

---

## 7️⃣ 모델 해석

### 7.1 Feature Importance

```python
import matplotlib.pyplot as plt

# Feature Importance 추출
importances = rf.feature_importances_
indices = np.argsort(importances)[::-1]

# 시각화
plt.figure(figsize=(10, 6))
plt.bar(range(len(importances)), importances[indices])
plt.xticks(range(len(importances)), 
           [feature_cols[i] for i in indices], 
           rotation=45, ha='right')
plt.xlabel('Feature')
plt.ylabel('Importance')
plt.title('Feature Importance')
plt.tight_layout()
plt.show()

# 상위 10개 Feature
for i in range(10):
    idx = indices[i]
    print(f"{i+1}. {feature_cols[idx]}: {importances[idx]:.4f}")
```

---

### 7.2 SHAP (SHapley Additive exPlanations)

```python
import shap

# SHAP Explainer 생성
explainer = shap.TreeExplainer(rf)
shap_values = explainer.shap_values(X_test)

# Summary Plot
shap.summary_plot(shap_values[1], X_test, feature_names=feature_cols)

# Force Plot (개별 예측 설명)
shap.force_plot(
    explainer.expected_value[1],
    shap_values[1][0],
    X_test.iloc[0],
    feature_names=feature_cols
)

# Dependence Plot (Feature와 Target 관계)
shap.dependence_plot(
    'feature_base_age',
    shap_values[1],
    X_test,
    feature_names=feature_cols
)
```

**해석**:
- **빨간색**: 고위험 방향으로 영향
- **파란색**: 저위험 방향으로 영향
- **크기**: 영향력의 크기

---

### 7.3 Partial Dependence Plot

```python
from sklearn.inspection import PartialDependenceDisplay

# Partial Dependence Plot
features = ['feature_base_age', 'feature_base_area_log']
PartialDependenceDisplay.from_estimator(
    rf, X_train, features, 
    grid_resolution=50
)
plt.tight_layout()
plt.show()
```

---

## 8️⃣ 모델 저장 & 배포

### 8.1 모델 저장

```python
import joblib

# 모델 저장
joblib.dump(rf, 'factory_risk_model_v1.pkl')

# 스케일러 저장
joblib.dump(scaler, 'scaler_v1.pkl')

# Feature 리스트 저장
with open('features_v1.txt', 'w') as f:
    f.write('\n'.join(feature_cols))

print("✅ 모델 저장 완료")
```

### 8.2 모델 로드 & 예측

```python
# 모델 로드
model = joblib.load('factory_risk_model_v1.pkl')
scaler = joblib.load('scaler_v1.pkl')

with open('features_v1.txt', 'r') as f:
    features = [line.strip() for line in f]

# 새 데이터 예측
def predict_risk(new_data):
    """새로운 공장 데이터의 리스크 예측"""
    # Feature 추출
    X_new = new_data[features]
    
    # 스케일링
    X_scaled = scaler.transform(X_new)
    
    # 예측
    prediction = model.predict(X_scaled)
    probability = model.predict_proba(X_scaled)[:, 1]
    
    return {
        'risk_label': '고위험' if prediction[0] == 1 else '저위험',
        'risk_probability': probability[0]
    }

# 사용 예시
result = predict_risk(new_factory_data)
print(f"리스크: {result['risk_label']}")
print(f"확률: {result['risk_probability']:.2%}")
```

---

## 9️⃣ 모델 모니터링

### 9.1 성능 모니터링

```python
class ModelMonitor:
    """모델 성능 모니터링"""
    
    def __init__(self, model, threshold=0.05):
        self.model = model
        self.baseline_auc = None
        self.threshold = threshold  # 허용 성능 저하
    
    def set_baseline(self, X_test, y_test):
        """기준선 설정"""
        y_pred_proba = self.model.predict_proba(X_test)[:, 1]
        self.baseline_auc = roc_auc_score(y_test, y_pred_proba)
        print(f"Baseline AUC: {self.baseline_auc:.3f}")
    
    def check_performance(self, X_new, y_new):
        """성능 체크"""
        y_pred_proba = self.model.predict_proba(X_new)[:, 1]
        current_auc = roc_auc_score(y_new, y_pred_proba)
        
        degradation = self.baseline_auc - current_auc
        
        if degradation > self.threshold:
            print(f"⚠️ 성능 저하 감지!")
            print(f"Baseline: {self.baseline_auc:.3f}")
            print(f"Current: {current_auc:.3f}")
            print(f"Degradation: {degradation:.3f}")
            return False
        else:
            print(f"✅ 성능 정상 (AUC: {current_auc:.3f})")
            return True

# 사용
monitor = ModelMonitor(rf)
monitor.set_baseline(X_test, y_test)
monitor.check_performance(X_new, y_new)
```

---

## 🎯 모델 선택 가이드

### 상황별 추천 모델

| 상황 | 추천 모델 | 이유 |
|------|----------|------|
| 빠른 프로토타입 | Logistic Regression | 빠르고 안정적 |
| 높은 정확도 필요 | Random Forest, XGBoost | 최고 성능 |
| 해석 중요 | Logistic Regression + SHAP | 설명 용이 |
| 대용량 데이터 | XGBoost | 메모리 효율적 |
| 불균형 데이터 | class_weight='balanced' | 클래스 균형 조정 |

---

## 📚 참고 자료

### 책
- "Hands-On Machine Learning" - Aurélien Géron
- "Introduction to Statistical Learning" - James et al.

### 온라인 코스
- Coursera: Machine Learning (Andrew Ng)
- Fast.ai: Practical Deep Learning

### 라이브러리 문서
- [scikit-learn](https://scikit-learn.org/)
- [XGBoost](https://xgboost.readthedocs.io/)
- [SHAP](https://shap.readthedocs.io/)

---

**AIRD ML Methodology v1.0**  
**작성일**: 2025-11-28  
**문의**: aird-support@example.com

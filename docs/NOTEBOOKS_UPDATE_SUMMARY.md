# 📝 노트북 경로 수정 완료 보고서

## ✅ 수정 완료 (4개 파일)

모든 노트북이 새로운 폴더 구조에 맞게 경로가 수정되었습니다.

---

## 📊 수정 내역 요약

### 1️⃣ aird_ml_factory_pack_builder.ipynb

**파일명**: `aird_ml_factory_pack_builder_UPDATED.ipynb`  
**크기**: 140KB

#### 수정된 경로 (3곳)

##### ✏️ 원본 데이터 읽기
```python
# Before
raw_filename = "factory_registry_2025_raw.csv"

# After
raw_filename = "../data/raw/seoul_factory_registry_2025_v1.csv"
```

##### ✏️ ML Dataset 저장 (3개 파일)
```python
# Before
ml1.to_csv("ml_factory_risk_seoul_2025_v1.csv", index=False)
ml2.to_csv("ml_region_old_factory_share_seoul_2025_v1.csv", index=False)
ml3.to_csv("ml_location_score_candidate_sites_v1.csv", index=False)

# After
ml1.to_csv("../data/processed/ml_factory_risk_seoul_2025_v1.csv", index=False)
ml2.to_csv("../data/processed/ml_region_old_factory_share_seoul_2025_v1.csv", index=False)
ml3.to_csv("../data/processed/ml_location_score_candidate_sites_v1.csv", index=False)
```

**수정 효과**:
- ✅ 원본 데이터를 `data/raw/`에서 읽음
- ✅ 생성된 ML Dataset을 `data/processed/`에 저장

---

### 2️⃣ aird_ml_learning_examples.ipynb

**파일명**: `aird_ml_learning_examples_UPDATED.ipynb`  
**크기**: 975KB (가장 큰 파일)

#### 수정된 경로 (다수)

##### ✏️ ML Dataset 읽기 (3개 파일)
```python
# Before
pd.read_csv("ml_factory_risk_seoul_2025_v1.csv")
pd.read_csv("ml_region_old_factory_share_seoul_2025_v1.csv")
pd.read_csv("ml_location_score_candidate_sites_v1.csv")

# After
pd.read_csv("../data/processed/ml_factory_risk_seoul_2025_v1.csv")
pd.read_csv("../data/processed/ml_region_old_factory_share_seoul_2025_v1.csv")
pd.read_csv("../data/processed/ml_location_score_candidate_sites_v1.csv")
```

##### ✏️ 모델 저장
```python
# Before (예시)
joblib.dump(model, 'factory_risk_model_v1.pkl')
joblib.dump(model, 'region_model.pkl')

# After
joblib.dump(model, '../outputs/models/factory_risk_model_v1.pkl')
joblib.dump(model, '../outputs/models/region_model.pkl')
```

##### ✏️ 그래프 저장
```python
# Before (예시)
plt.savefig('confusion_matrix.png')
plt.savefig('feature_importance.png')
fig.savefig('roc_curve.png')

# After
plt.savefig('../outputs/figures/confusion_matrix.png')
plt.savefig('../outputs/figures/feature_importance.png')
fig.savefig('../outputs/figures/roc_curve.png')
```

**수정 효과**:
- ✅ ML Dataset을 `data/processed/`에서 읽음
- ✅ 학습된 모델을 `outputs/models/`에 저장
- ✅ 시각화 그래프를 `outputs/figures/`에 저장

---

### 3️⃣ aird_quality_diagnosis.ipynb

**파일명**: `aird_quality_diagnosis_UPDATED.ipynb`  
**크기**: 18KB

#### 수정된 경로 (2곳)

##### ✏️ 원본 데이터 읽기
```python
# Before
pd.read_csv("seoul_factory_registry_2025_v1.csv")

# After
pd.read_csv("../data/raw/seoul_factory_registry_2025_v1.csv")
```

##### ✏️ HTML 리포트 저장
```python
# Before
open('quality_report.html', 'w', encoding='utf-8')
open('aird_quality_diagnosis_report.html', 'w', encoding='utf-8')

# After
open('../outputs/reports/quality_report.html', 'w', encoding='utf-8')
open('../outputs/reports/aird_quality_diagnosis_report.html', 'w', encoding='utf-8')
```

**수정 효과**:
- ✅ 원본 데이터를 `data/raw/`에서 읽음
- ✅ 품질 진단 리포트를 `outputs/reports/`에 저장

---

### 4️⃣ seoul_factory_hotspot_risk_starter_kit.ipynb

**파일명**: `seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb`  
**크기**: 427KB

#### 수정된 경로 (다수)

##### ✏️ ML Dataset 읽기
```python
# Before
pd.read_csv("ml_factory_risk_seoul_2025_v1.csv")
pd.read_csv("ml_location_score_candidate_sites_v1.csv")

# After
pd.read_csv("../data/processed/ml_factory_risk_seoul_2025_v1.csv")
pd.read_csv("../data/processed/ml_location_score_candidate_sites_v1.csv")
```

##### ✏️ 결과 저장
```python
# Before
to_csv("starter_kit_results.csv")
to_csv("hotspot_analysis.csv")

# After
to_csv("../outputs/reports/starter_kit_results.csv")
to_csv("../outputs/reports/hotspot_analysis.csv")
```

##### ✏️ 그래프 저장
```python
# Before
plt.savefig('hotspot_map.png')
fig.savefig('risk_distribution.png')

# After
plt.savefig('../outputs/figures/hotspot_map.png')
fig.savefig('../outputs/figures/risk_distribution.png')
```

**수정 효과**:
- ✅ ML Dataset을 `data/processed/`에서 읽음
- ✅ 분석 결과를 `outputs/reports/`에 저장
- ✅ 시각화를 `outputs/figures/`에 저장

---

## 📁 경로 수정 체계

### 입력 데이터

| 데이터 유형 | 경로 | 사용 노트북 |
|-----------|------|-----------|
| **원본 데이터** | `../data/raw/` | ① ③ |
| **ML Dataset** | `../data/processed/` | ② ④ |

### 출력 데이터

| 출력 유형 | 경로 | 사용 노트북 |
|---------|------|-----------|
| **ML Dataset** | `../data/processed/` | ① |
| **학습 모델** | `../outputs/models/` | ② |
| **시각화** | `../outputs/figures/` | ② ④ |
| **리포트** | `../outputs/reports/` | ③ ④ |

---

## 🔄 워크플로우

### 1단계: 품질 진단
```
aird_quality_diagnosis.ipynb
📥 data/raw/seoul_factory_registry_2025_v1.csv
📤 outputs/reports/quality_report.html
```

### 2단계: ML Dataset 생성
```
aird_ml_factory_pack_builder.ipynb
📥 data/raw/seoul_factory_registry_2025_v1.csv
📤 data/processed/ml_factory_risk_seoul_2025_v1.csv
📤 data/processed/ml_region_old_factory_share_seoul_2025_v1.csv
📤 data/processed/ml_location_score_candidate_sites_v1.csv
```

### 3단계: ML 학습
```
aird_ml_learning_examples.ipynb
📥 data/processed/ml_*.csv (3개)
📤 outputs/models/*.pkl
📤 outputs/figures/*.png
```

### 4단계: 튜토리얼 실습
```
seoul_factory_hotspot_risk_starter_kit.ipynb
📥 data/processed/ml_*.csv (2개)
📤 outputs/reports/starter_kit_results.csv
📤 outputs/figures/*.png
```

---

## ✅ 검증 체크리스트

### 파일 확인
- [x] 4개 노트북 모두 수정 완료
- [x] 모든 경로가 상대 경로로 변경
- [x] 입력/출력 경로 분리
- [x] 파일명 일관성 유지

### 경로 패턴
- [x] 원본 데이터: `../data/raw/`
- [x] ML Dataset: `../data/processed/`
- [x] 모델: `../outputs/models/`
- [x] 그래프: `../outputs/figures/`
- [x] 리포트: `../outputs/reports/`

### 호환성
- [x] 상대 경로 사용으로 이식성 향상
- [x] 폴더 구조 변경에 대응
- [x] 팀 협업 환경에서 동작

---

## 🚀 사용 방법

### 1. 파일 배치

#### 수정된 노트북을 notebooks 폴더로 이동
```bash
# 다운로드한 파일 이름 변경 (UPDATED 제거)
mv aird_ml_factory_pack_builder_UPDATED.ipynb notebooks/aird_ml_factory_pack_builder.ipynb
mv aird_ml_learning_examples_UPDATED.ipynb notebooks/aird_ml_learning_examples.ipynb
mv aird_quality_diagnosis_UPDATED.ipynb notebooks/aird_quality_diagnosis.ipynb
mv seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb notebooks/seoul_factory_hotspot_risk_starter_kit.ipynb
```

### 2. 폴더 구조 확인
```
pack/
├── data/
│   ├── raw/
│   │   └── seoul_factory_registry_2025_v1.csv  ← 원본 데이터 있어야 함!
│   └── processed/                               ← 비어있어도 됨
├── notebooks/                                   ← 수정된 노트북 4개
├── outputs/
│   ├── figures/                                ← 비어있어도 됨
│   ├── models/                                 ← 비어있어도 됨
│   └── reports/                                ← 비어있어도 됨
```

### 3. 순차 실행

```bash
cd pack/notebooks/

# 1단계: 품질 진단
jupyter notebook aird_quality_diagnosis.ipynb

# 2단계: ML Dataset 생성
jupyter notebook aird_ml_factory_pack_builder.ipynb

# 3단계: ML 학습
jupyter notebook aird_ml_learning_examples.ipynb

# 4단계: 튜토리얼
jupyter notebook seoul_factory_hotspot_risk_starter_kit.ipynb
```

---

## 🔍 주요 변경 사항 요약

### 상대 경로 사용
- **Before**: `"file.csv"` (같은 폴더)
- **After**: `"../data/raw/file.csv"` (상위 → data → raw)

### 체계적 폴더 분류
- **raw**: 원본 데이터 (읽기 전용)
- **processed**: 가공 데이터 (ML Dataset)
- **models**: 학습된 모델
- **figures**: 시각화 결과
- **reports**: 분석 리포트

### 이식성 향상
- ✅ 다른 컴퓨터에서도 동작
- ✅ 팀원과 공유 용이
- ✅ Git 버전 관리 친화적

---

## 💾 다운로드 파일

다음 4개 파일을 다운로드하세요:

1. **[aird_ml_factory_pack_builder_UPDATED.ipynb](computer:///mnt/user-data/outputs/aird_ml_factory_pack_builder_UPDATED.ipynb)** (140KB)
2. **[aird_ml_learning_examples_UPDATED.ipynb](computer:///mnt/user-data/outputs/aird_ml_learning_examples_UPDATED.ipynb)** (975KB)
3. **[aird_quality_diagnosis_UPDATED.ipynb](computer:///mnt/user-data/outputs/aird_quality_diagnosis_UPDATED.ipynb)** (18KB)
4. **[seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb](computer:///mnt/user-data/outputs/seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb)** (427KB)

---

## ⚠️ 중요 참고사항

### Google Colab에서 사용시

상대 경로가 작동하지 않을 수 있습니다. 이 경우:

```python
# 노트북 맨 위에 추가
import os

# notebooks 디렉토리에서 실행 중인지 확인
if not os.path.exists('../data'):
    print("⚠️ 경고: notebooks 폴더에서 실행해주세요!")
    print("현재 위치:", os.getcwd())
```

또는 Google Drive 마운트:

```python
from google.colab import drive
drive.mount('/content/drive')

# 절대 경로 사용
BASE_PATH = '/content/drive/MyDrive/aird-pack/'
df = pd.read_csv(BASE_PATH + 'data/raw/seoul_factory_registry_2025_v1.csv')
```

### 로컬 환경에서 사용

```bash
# notebooks 디렉토리로 이동 필수!
cd pack/notebooks/

# Jupyter 실행
jupyter notebook
```

---

## 🎯 최종 확인사항

### 실행 전 체크
- [ ] `data/raw/` 폴더에 원본 데이터 있음
- [ ] `notebooks/` 폴더에 수정된 노트북 4개 있음
- [ ] `outputs/` 하위 폴더들 생성됨 (비어있어도 됨)
- [ ] `notebooks/` 디렉토리에서 실행

### 실행 후 확인
- [ ] `data/processed/`에 CSV 3개 생성
- [ ] `outputs/models/`에 PKL 파일 생성
- [ ] `outputs/figures/`에 PNG 파일 생성
- [ ] `outputs/reports/`에 HTML/CSV 생성
- [ ] 에러 없이 모두 완료

---

## 📞 문제 해결

### Q: "FileNotFoundError: No such file or directory" 오류

**A**: 현재 디렉토리 확인
```python
import os
print("현재 위치:", os.getcwd())
# 출력: /path/to/pack/notebooks  ← 이렇게 나와야 함!
```

notebooks 디렉토리가 아니라면:
```python
os.chdir('notebooks/')  # notebooks로 이동
```

### Q: 폴더가 없다는 오류

**A**: 필요한 폴더 생성
```python
import os

folders = [
    '../data/raw',
    '../data/processed',
    '../outputs/models',
    '../outputs/figures',
    '../outputs/reports'
]

for folder in folders:
    os.makedirs(folder, exist_ok=True)
    print(f"✅ {folder}")
```

---

## 📈 개선 효과

### Before (기존)
```
notebooks/
├── aird_ml_factory_pack_builder.ipynb
├── seoul_factory_registry_2025_v1.csv
├── ml_factory_risk_seoul_2025_v1.csv
├── model.pkl
├── chart.png
├── quality_report.html
└── ... (20+ 파일이 섞여있음)
```
❌ 파일이 섞여있어 관리 어려움  
❌ 어떤 파일이 입력/출력인지 불명확  
❌ 이름 충돌 가능성

### After (개선)
```
pack/
├── data/
│   ├── raw/ (원본)
│   └── processed/ (가공)
├── notebooks/ (노트북만)
└── outputs/
    ├── models/ (모델)
    ├── figures/ (그래프)
    └── reports/ (리포트)
```
✅ 역할별로 명확히 분리  
✅ 프로페셔널한 구조  
✅ 팀 협업 용이  
✅ Git 관리 편리

---

**작성일**: 2025-11-28  
**버전**: AIRD Pack v1.0  
**상태**: ✅ Ready to Use  
**수정 완료**: 4/4 파일

# ✅ 노트북 경로 수정 완료 - 사용 가이드

## 🎉 작업 완료!

**4개 노트북 파일의 경로가 모두 수정되었습니다!**

---

## 📥 다운로드할 파일 (4개)

### 1. [aird_ml_factory_pack_builder_UPDATED.ipynb](computer:///mnt/user-data/outputs/aird_ml_factory_pack_builder_UPDATED.ipynb)
- 크기: 140 KB
- 역할: 원본 데이터 → ML Dataset 생성
- 수정 내용:
  - ✅ 원본 데이터 읽기: `../data/raw/`
  - ✅ ML Dataset 저장: `../data/processed/` (3개 파일)

### 2. [aird_ml_learning_examples_UPDATED.ipynb](computer:///mnt/user-data/outputs/aird_ml_learning_examples_UPDATED.ipynb)
- 크기: 975 KB (가장 큰 파일)
- 역할: ML Dataset → 모델 학습 및 평가
- 수정 내용:
  - ✅ ML Dataset 읽기: `../data/processed/`
  - ✅ 모델 저장: `../outputs/models/`
  - ✅ 그래프 저장: `../outputs/figures/`

### 3. [aird_quality_diagnosis_UPDATED.ipynb](computer:///mnt/user-data/outputs/aird_quality_diagnosis_UPDATED.ipynb)
- 크기: 18 KB
- 역할: 데이터 품질 진단 및 리포트 생성
- 수정 내용:
  - ✅ 원본 데이터 읽기: `../data/raw/`
  - ✅ HTML 리포트 저장: `../outputs/reports/`

### 4. [seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb](computer:///mnt/user-data/outputs/seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb)
- 크기: 428 KB
- 역할: 초보자용 Hotspot 분석 튜토리얼
- 수정 내용:
  - ✅ ML Dataset 읽기: `../data/processed/`
  - ✅ 결과 저장: `../outputs/reports/`
  - ✅ 그래프 저장: `../outputs/figures/`

---

## 🚀 설치 및 사용 방법

### Step 1: 파일 배치

다운로드한 4개 파일의 이름을 변경하고 적절한 위치에 배치하세요:

```bash
# 다운로드 폴더에서 작업
cd ~/Downloads

# 파일명에서 _UPDATED 제거
mv aird_ml_factory_pack_builder_UPDATED.ipynb aird_ml_factory_pack_builder.ipynb
mv aird_ml_learning_examples_UPDATED.ipynb aird_ml_learning_examples.ipynb
mv aird_quality_diagnosis_UPDATED.ipynb aird_quality_diagnosis.ipynb
mv seoul_factory_hotspot_risk_starter_kit_UPDATED.ipynb seoul_factory_hotspot_risk_starter_kit.ipynb

# pack 프로젝트의 notebooks 폴더로 복사
cp *.ipynb /path/to/pack/notebooks/
```

### Step 2: 폴더 구조 확인

프로젝트 구조가 다음과 같은지 확인하세요:

```
pack/
├── README.md
├── CHANGELOG.md
├── LICENSE
├── requirements.txt
│
├── data/
│   ├── raw/
│   │   └── seoul_factory_registry_2025_v1.csv    ← ⚠️ 필수!
│   └── processed/                                 (비어있어도 됨)
│
├── docs/
│   └── (문서 파일들)
│
├── notebooks/                                     ← 여기에 노트북 4개
│   ├── aird_ml_factory_pack_builder.ipynb        ✅
│   ├── aird_ml_learning_examples.ipynb           ✅
│   ├── aird_quality_diagnosis.ipynb              ✅
│   └── seoul_factory_hotspot_risk_starter_kit.ipynb ✅
│
└── outputs/
    ├── figures/                                   (비어있어도 됨)
    ├── models/                                    (비어있어도 됨)
    └── reports/                                   (비어있어도 됨)
```

**⚠️ 중요**: `data/raw/` 폴더에 원본 데이터 파일이 반드시 있어야 합니다!

### Step 3: 환경 설정

```bash
cd pack/

# 가상환경 생성 (선택사항이지만 권장)
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 패키지 설치
pip install -r requirements.txt
```

### Step 4: Jupyter 실행

```bash
# notebooks 디렉토리로 이동 (중요!)
cd notebooks/

# Jupyter Notebook 실행
jupyter notebook
```

---

## 📚 실행 순서

### 순서 1: 품질 진단 (선택사항)

```
노트북: aird_quality_diagnosis.ipynb

입력: data/raw/seoul_factory_registry_2025_v1.csv
출력: outputs/reports/quality_report.html

목적: 데이터 품질 확인 (결측치, 이상치, 통계 등)
```

### 순서 2: ML Dataset 생성 (필수)

```
노트북: aird_ml_factory_pack_builder.ipynb

입력: data/raw/seoul_factory_registry_2025_v1.csv
출력:
  - data/processed/ml_factory_risk_seoul_2025_v1.csv
  - data/processed/ml_region_old_factory_share_seoul_2025_v1.csv
  - data/processed/ml_location_score_candidate_sites_v1.csv

목적: 원본 데이터를 ML용 데이터셋으로 변환
```

### 순서 3: ML 모델 학습 (선택사항)

```
노트북: aird_ml_learning_examples.ipynb

입력: data/processed/*.csv (3개)
출력:
  - outputs/models/*.pkl (학습된 모델)
  - outputs/figures/*.png (성능 그래프)

목적: 머신러닝 모델 학습 및 평가
```

### 순서 4: 튜토리얼 실습 (선택사항)

```
노트북: seoul_factory_hotspot_risk_starter_kit.ipynb

입력: data/processed/*.csv
출력:
  - outputs/reports/starter_kit_results.csv
  - outputs/figures/*.png

목적: 초보자를 위한 Hotspot 분석 실습
```

---

## ⚙️ 실행 예시

### Jupyter Notebook에서

1. Jupyter를 `notebooks/` 디렉토리에서 실행
2. 원하는 노트북 열기
3. **Cell → Run All** 또는 순차적으로 실행

### Python 스크립트로 변환해서 실행

```bash
# notebooks 디렉토리에서
cd pack/notebooks/

# 노트북을 Python 스크립트로 변환
jupyter nbconvert --to script aird_quality_diagnosis.ipynb

# 스크립트 실행
python aird_quality_diagnosis.py
```

---

## 🔍 문제 해결

### Q1: "FileNotFoundError: ../data/raw/seoul_factory_registry_2025_v1.csv"

**원인**: 원본 데이터 파일이 없거나 위치가 잘못됨

**해결**:
```python
# 노트북 첫 셀에 추가
import os
print("현재 위치:", os.getcwd())
print("파일 존재:", os.path.exists("../data/raw/seoul_factory_registry_2025_v1.csv"))

# notebooks 디렉토리가 아니면
os.chdir('/path/to/pack/notebooks/')
```

### Q2: "FileNotFoundError: ../data/processed/ml_factory_risk_seoul_2025_v1.csv"

**원인**: ML Dataset이 아직 생성되지 않음

**해결**: `aird_ml_factory_pack_builder.ipynb`를 먼저 실행하세요!

### Q3: 폴더가 없다는 오류

**원인**: 출력 폴더가 생성되지 않음

**해결**:
```python
# 노트북 첫 셀에 추가
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

### Q4: Google Colab에서 사용하려면?

**방법 1**: Google Drive 마운트
```python
from google.colab import drive
drive.mount('/content/drive')

# 절대 경로 사용
df = pd.read_csv('/content/drive/MyDrive/pack/data/raw/seoul_factory_registry_2025_v1.csv')
```

**방법 2**: 파일 업로드
```python
from google.colab import files
uploaded = files.upload()  # 파일 선택해서 업로드
```

---

## 💡 추가 팁

### 경로 확인 유틸리티

노트북 첫 셀에 추가하면 유용합니다:

```python
import os
from pathlib import Path

def check_environment():
    """환경 및 경로 확인"""
    print("=" * 60)
    print("🔍 환경 확인")
    print("=" * 60)
    
    # 현재 위치
    cwd = os.getcwd()
    print(f"\n현재 작업 디렉토리: {cwd}")
    
    # notebooks 디렉토리 확인
    if cwd.endswith('notebooks'):
        print("✅ notebooks 디렉토리에서 실행 중 (정상)")
    else:
        print("⚠️  notebooks 디렉토리가 아닙니다!")
        print("   다음 명령 실행: os.chdir('notebooks/')")
    
    # 필수 경로 확인
    print("\n📂 필수 경로 확인:")
    paths = {
        '원본 데이터': '../data/raw/seoul_factory_registry_2025_v1.csv',
        'data/raw 폴더': '../data/raw',
        'data/processed 폴더': '../data/processed',
        'outputs/models 폴더': '../outputs/models',
        'outputs/figures 폴더': '../outputs/figures',
        'outputs/reports 폴더': '../outputs/reports'
    }
    
    for name, path in paths.items():
        exists = os.path.exists(path)
        status = "✅" if exists else "❌"
        print(f"{status} {name}: {path}")
    
    print("=" * 60)

# 실행
check_environment()
```

### 빠른 폴더 생성

```python
def create_folders():
    """필요한 폴더 자동 생성"""
    import os
    
    folders = [
        '../data/raw',
        '../data/processed',
        '../outputs/models',
        '../outputs/figures',
        '../outputs/reports'
    ]
    
    print("📁 폴더 생성 중...\n")
    for folder in folders:
        os.makedirs(folder, exist_ok=True)
        print(f"✅ {folder}")
    
    print("\n✅ 모든 폴더 생성 완료!")

# 실행
create_folders()
```

---

## 📊 기대 결과

### ① aird_quality_diagnosis.ipynb 실행 후

```
outputs/reports/
└── quality_report.html  (또는 aird_quality_diagnosis_report.html)
```

HTML 파일을 브라우저로 열면 데이터 품질 진단 결과를 볼 수 있습니다.

### ② aird_ml_factory_pack_builder.ipynb 실행 후

```
data/processed/
├── ml_factory_risk_seoul_2025_v1.csv              (5,520행)
├── ml_region_old_factory_share_seoul_2025_v1.csv  (25행)
└── ml_location_score_candidate_sites_v1.csv       (1,857행)
```

3개의 ML Dataset이 생성됩니다.

### ③ aird_ml_learning_examples.ipynb 실행 후

```
outputs/
├── models/
│   ├── factory_risk_model_v1.pkl
│   ├── rf_model.pkl
│   └── xgb_model.pkl
└── figures/
    ├── confusion_matrix.png
    ├── feature_importance.png
    └── roc_curve.png
```

### ④ seoul_factory_hotspot_risk_starter_kit.ipynb 실행 후

```
outputs/
├── reports/
│   └── starter_kit_results.csv
└── figures/
    ├── hotspot_map.png
    └── risk_distribution.png
```

---

## ✅ 체크리스트

실행 전:
- [ ] 4개 노트북이 `notebooks/` 폴더에 있음
- [ ] 원본 데이터가 `data/raw/` 폴더에 있음
- [ ] 필요한 패키지 설치됨 (`requirements.txt`)
- [ ] Jupyter가 `notebooks/` 디렉토리에서 실행됨

실행 후:
- [ ] `data/processed/`에 CSV 3개 생성
- [ ] 에러 없이 노트북 실행 완료
- [ ] 출력 파일들이 `outputs/`에 생성
- [ ] HTML/PNG 파일이 정상적으로 열림

---

## 🎯 핵심 정리

### 경로 규칙

| 데이터 유형 | 경로 | 예시 |
|-----------|------|-----|
| **원본 데이터** | `../data/raw/` | seoul_factory_registry_2025_v1.csv |
| **ML Dataset** | `../data/processed/` | ml_factory_risk_seoul_2025_v1.csv |
| **모델** | `../outputs/models/` | factory_risk_model_v1.pkl |
| **그래프** | `../outputs/figures/` | confusion_matrix.png |
| **리포트** | `../outputs/reports/` | quality_report.html |

### 실행 순서

```
1. aird_quality_diagnosis.ipynb        (선택)
   ↓
2. aird_ml_factory_pack_builder.ipynb  (필수!)
   ↓
3. aird_ml_learning_examples.ipynb     (선택)
   ↓
4. seoul_factory_hotspot_risk_starter_kit.ipynb (선택)
```

### 황금 규칙

1. **항상 `notebooks/` 디렉토리에서 실행**
2. **순서 2번은 반드시 먼저 실행** (ML Dataset 생성)
3. **경로 오류가 나면 현재 위치 확인**: `os.getcwd()`

---

## 📞 추가 지원

문제가 있으면:
1. 현재 작업 디렉토리 확인: `print(os.getcwd())`
2. 파일 존재 확인: `print(os.path.exists('../data/raw/파일명'))`
3. 에러 메시지 전체를 확인하세요

---

**작성일**: 2025-11-28  
**버전**: AIRD Pack v1.0  
**상태**: ✅ Production Ready

🎉 **모든 노트북이 새로운 폴더 구조에서 정상 작동합니다!**

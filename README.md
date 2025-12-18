# AIRD Pack

서울시 공장 등록 데이터를 활용한 머신러닝 분석과 AI 에이전트 프로젝트입니다. 공장 리스크 분류, 노후공장 비중 분석, Hotspot 탐지 등의 ML 모델과 자연어 질의응답을 통한 데이터 분석 기능을 제공합니다.

## GitHub Repository

[https://github.com/hike-lab/AIRD-PACK](https://github.com/hike-lab/AIRD-PACK)

## 목차

1. [사전 요구사항](#1-사전-요구사항)
2. [파일 구조](#2-파일-구조)
3. [실행 방법](#3-실행-방법)
   - [3.1 로컬 실행](#31-로컬-실행)
   - [3.2 Google Colab 실행](#32-google-colab-실행)
4. [사용 가이드](#4-사용-가이드)
   - [4.1 데이터](#41-데이터)
   - [4.2 ML-Pack](#42-ml-pack)
   - [4.3 QA-Pack](#43-qa-pack)
5. [주의 사항](#5-주의-사항)

---

## 1. 사전 요구사항

- Python 3.12 이상
- OpenAI API Key

## 2. 파일 구조

```
AIRD-PACK
│
├── README.md
├── requirements.txt                    # 라이브러리 설치 파일
├── .env.example                        # API KEY 설정 파일. `.env`로 파일명 변경 후 사용
│
├── data
│   ├── raw/
│   │   └── seoul_factory_registry_2025_v1.csv          # 서울시 공장등록 원본
│   └── processed/                                      # ml-pack 코드 실행 파일이 저장되는 경로
│       ├── ml_factory_risk_seoul_2025_v1.csv           # 공장 리스크
│       ├── ml_region_old_factory_share_seoul_2025_v1.csv  # 자치구 노후비중
│       └── ml_location_score_candidate_sites_v1.csv   # 동 단위 Hotspot
│
└── code
    ├── ml-pack
    │   ├── aird_quality_diagnosis.ipynb                # 품질진단 노트북
    │   ├── aird_ml_factory_pack_builder.ipynb          # ML Dataset 빌더
    │   ├── aird_ml_learning_examples.ipynb             # ML 학습 예제
    │   └── aird_ml_inference_examples.ipynb            # 학습 모델 추론 예제
    └── qa-pack
        ├── prompt-test.ipynb                           # 프롬프트 테스트 노트북
        └── sub-files/                                  # QA-Pack 서브 모듈
            ├── agent_module.py
            ├── config.py
            ├── state.py
            └── tools_module.py
```

## 3. 실행 방법

이 프로젝트는 **로컬 환경**과 **Google Colab** 두 가지 방식으로 실행할 수 있습니다.

### 3.1 로컬 실행

#### Step 1: 가상환경 생성 및 활성화

**macOS / Linux**
```bash
python -m venv env
source env/bin/activate
```

**Windows**
```bash
python -m venv env
env\Scripts\activate
```

#### Step 2: 의존성 패키지 설치

```bash
pip install -r requirements.txt
```

#### Step 3: 환경 변수 설정

프로젝트 루트 디렉토리에 `.env` 파일을 생성하고 OpenAI API Key를 설정합니다:

```bash
OPENAI_API_KEY=your_openai_api_key_here
```

### 3.2 Google Colab 실행

아래 노트북 URL을 Google Colab에서 열어 실행할 수 있습니다.

#### ML-Pack 

1. **데이터 품질 진단**
   - [🔗 Colab URL](https://colab.research.google.com/drive/14OWqLVBdiHT44wAkDPiXWD_2nitRFMEZ)
   - 파일: `code/ml-pack/aird_quality_diagnosis.ipynb`

2. **ML Dataset 빌더**
   - [🔗 Colab URL](https://colab.research.google.com/drive/1E_oFb_-hHXo4QlQK4hKBbZx4EuxFUqJj)
   - 파일: `code/ml-pack/aird_ml_factory_pack_builder.ipynb`

3. **ML 학습 예제**
   - [🔗 Colab URL](https://colab.research.google.com/drive/1QqkyWrdElMyGMVGrQAFaHYWuK0ch79cL)
   - 파일: `code/ml-pack/aird_ml_learning_examples.ipynb`

4. **학습 모델 추론 예제**
   - [🔗 Colab URL](https://colab.research.google.com/drive/1oS5FomJoCgra6T5U70ZdhGjPc4ZmnSDL)
   - 파일: `code/ml-pack/aird_ml_inference_examples.ipynb`

#### QA-Pack 

1. **프롬프트 테스트**
   - [🔗 Colab URL](https://colab.research.google.com/drive/17XI7i5CAoi5XTRmv4pjEIL86L8nBQMUb)
   - 파일: `code/qa-pack/prompt-test.ipynb`

> **참고**: Colab에서 실행 시 환경 변수는 노트북 내에서 직접 설정하거나 Colab의 Secrets 기능을 활용하세요.

---

## 4. 사용 가이드

### 4.1 데이터

- 한국산업단지공단의 [공장등록통계](https://www.factoryon.go.kr/bbs/frtblRecsroomBbsDetail.do?searchBbsCode=&searchBbsMenu=S&selectBbsSn=1040&pageIndex=1&scroll=false&pageUnit=10&searchCondition=01&searchKeyword=) 데이터(2025년 10월말 기준)를 사용합니다.
- `data/raw/seoul_factory_registry_2025_v1.csv`는 '(2025.10월말기준)_공장등록통계.xlsx' 파일을 전처리하고, 서울특별시 데이터만 추출하여 가공한 데이터입니다.

### 4.2 ML-Pack

#### ✅ 테스트 파일 (순서대로 실행)

1. `code/ml-pack/aird_quality_diagnosis.ipynb` - 데이터 품질 진단
2. `code/ml-pack/aird_ml_factory_pack_builder.ipynb` - ML Dataset 빌더
3. `code/ml-pack/aird_ml_learning_examples.ipynb` - ML 학습 예제 (3가지 모델)
4. `code/ml-pack/aird_ml_inference_examples.ipynb` - 학습 모델 기반 추론 예제

#### ✅ 주요 기능

**1. 품질진단**
- 완전성, 정확성, 일관성, 적시성, 유효성 평가
- 종합 품질 점수 산출 (0-100점)
- HTML 리포트 자동 생성

**2. AI Ready 변환**
- 3개 ML Dataset 생성

**3. ML 학습 & 해석**
- Random Forest, XGBoost 학습
- SHAP 기반 해석

**4. 시각화**
- 차트 (Matplotlib, Plotly)

**5. 인사이트 도출**
- 정책: 위험 지역 발굴, 예산 배분
- 민간: 보험 리스크, ESG 컨설팅, 입지 전략

#### ✅ 사용 예시

**ML 파이프라인**
- 데이터 전처리: 결측치 처리, 이상치 제거, Feature Engineering
- 모델 학습: Logistic Regression, Random Forest, XGBoost
- 모델 해석: SHAP, Feature Importance, Partial Dependence
- 하이퍼파라미터 튜닝: Grid Search, Random Search
- 모델 평가: Confusion Matrix, ROC-AUC, Precision/Recall

**1. 공장 리스크 분류**
- Dataset: `ml_factory_risk_seoul_2025_v1.csv` (5,521개)
- Target: `label_high_risk` (고위험/저위험)
- 모델: Random Forest (Accuracy ~85%)
- 활용: 안전 점검 우선순위, 보험 리스크 평가

**2. 자치구 노후공장 비중 분석**
- Dataset: `ml_region_old_factory_share_seoul_2025_v1.csv` (26개)
- Target: `label_high_old_share` (노후비중 高/低)
- 모델: Logistic Regression, Random Forest
- 활용: 정책 우선순위, 산업단지 재생

**3. 동 단위 Hotspot 탐지**
- Dataset: `ml_location_score_candidate_sites_v1.csv` (1,858개)
- Target: `label_hotspot` (Hotspot 여부)
- 모델: Random Forest
- 활용: 제조업 입지 전략, 인프라 투자

### 4.3 QA-Pack

공장 등록 현황 데이터를 분석하는 AI 에이전트를 실행하는 프로젝트입니다.

#### ✅ 실행 방법

`code/qa-pack/prompt-test.ipynb`에서 실행합니다.

1. **Setting & Import** 셀을 먼저 실행하여 필요한 모듈을 임포트합니다.
2. **Test** 셀을 실행하면 대화형 모드가 시작됩니다.
3. 질문을 입력하면 AI 에이전트가 데이터를 분석하여 답변을 제공합니다.
4. 종료하려면 `이제 끝!`을 입력합니다.

#### ✅ 주요 기능

- **대화형 인터페이스**: 연속적인 질문-답변 대화 지원
- **세션 관리**: 대화 히스토리를 유지하여 맥락 이해
- **자동 코드 생성**: 질문에 맞는 Pandas 코드 자동 생성 및 실행
- **결과 저장**: 생성된 코드와 실행 결과를 저장하여 추후 조회 가능

#### ✅ 사용 예시

노트북의 **Examples** 섹션에서 다음 예시들을 확인할 수 있습니다:

- "서울에서 공장이 가장 많은 구는 어디야?"
- "지난 20년간 서울에서 공장 수가 가장 많이 증가한 5개 구를 알려줘"
- "새로 생긴 공장들이 요즘 어느 지역에 많이 몰려 있어?"
- "금천구에서 규모가 가장 큰 회사는 어디야?"
- "공장의 면적 대비 직원 수가 많은 공장 상위 5개를 알려줘"

---

## 5. 주의 사항

- `.env` 파일에 올바른 OpenAI API Key가 설정되어 있어야 합니다.
- `code/qa-pack/sub-files` 폴더에 .py 파일들이 존재해야 합니다.
- 첫 실행 시 필요한 패키지 설치에 시간이 걸릴 수 있습니다.

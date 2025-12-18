# AIRD Pack 개요

## 🎯 AIRD Pack이란?

**AIRD (AI-Ready Data) Pack**은 공공데이터를 AI/ML 분석에 즉시 활용할 수 있도록  
**"원본 데이터 → 품질진단 → AI Ready 변환 → ML 학습·해석 → 인사이트"**  
전 과정을 표준화한 데이터 패키지입니다.

### 핵심 가치

1. **즉시 사용 가능**: 데이터 전처리 완료, 바로 ML 모델 학습 가능
2. **품질 보증**: 5대 품질 차원 검증 완료
3. **재현 가능**: 전 과정 코드 공개, 누구나 재현 가능
4. **확장 가능**: 다른 지역, 다른 데이터로 쉽게 확장

---

## 📦 AIRD Pack 구성요소

### 1. 원본 데이터 (Raw Data)
- **파일**: `seoul_factory_registry_2025_v1.csv`
- **설명**: 서울시 공장등록 원본 데이터 (11,227개 공장)
- **출처**: 전국 공장등록현황 (공공데이터포털)

### 2. 품질진단 리포트 (Quality Diagnosis)
- **도구**: `aird_quality_diagnosis.ipynb`
- **산출물**: HTML 품질 리포트, 품질 점수 (0-100점)
- **평가 차원**: 완전성, 정확성, 일관성, 적시성, 유효성

### 3. ML Dataset (AI-Ready Data)
3개의 Task별 ML Dataset 제공:

#### (1) 공장 리스크 예측용
- **파일**: `ml_factory_risk_seoul_2025_v1.csv`
- **단위**: 공장 (5,521개)
- **Target**: label_high_risk (고위험/저위험)
- **활용**: 보험 리스크 평가, 안전 점검 우선순위

#### (2) 자치구 노후공장 비중 분석용
- **파일**: `ml_region_old_factory_share_seoul_2025_v1.csv`
- **단위**: 자치구 (26개)
- **Target**: label_high_old_share (노후비중 高/低)
- **활용**: 지역 정책 우선순위, 산업단지 재생

#### (3) 동 단위 Hotspot 분석용
- **파일**: `ml_location_score_candidate_sites_v1.csv`
- **단위**: 동 (1,858개)
- **Target**: label_hotspot (Hotspot 여부)
- **활용**: 제조업 입지 전략, 인프라 투자

### 4. ML Pack 빌더 (Builder)
- **도구**: `aird_ml_factory_pack_builder.ipynb`
- **기능**: 원본 → ML Dataset 자동 변환
- **Feature**: Base, Aggregation, Old, Density 4개 그룹

### 5. ML 학습 예제 (Learning Examples)
- **도구**: `aird_ml_learning_examples.ipynb`
- **모델**: Logistic Regression, Random Forest, XGBoost
- **해석**: Feature Importance, SHAP
- **시각화**: 자치구/동 리스크 프로파일

### 6. Starter Kit (Quick Start)
- **도구**: `seoul_factory_hotspot_risk_starter_kit.ipynb`
- **대상**: 초보자용 End-to-End 튜토리얼
- **스타일**: Kaggle 스타일, 단계별 설명

---

## 🔄 AIRD Pack 워크플로우

```
┌─────────────────────────────────────────────────────────────┐
│                     AIRD Pack 워크플로우                       │
└─────────────────────────────────────────────────────────────┘

1. 원본 데이터 수집
   ├─ 공공데이터포털 다운로드
   └─ seoul_factory_registry_2025_v1.csv

            ↓

2. 품질진단 (NEW! ⭐)
   ├─ aird_quality_diagnosis.ipynb 실행
   ├─ 5대 차원 평가 (완전성/정확성/일관성/적시성/유효성)
   ├─ 종합 품질 점수 산출
   └─ HTML 리포트 생성

            ↓

3. AI Ready 변환
   ├─ aird_ml_factory_pack_builder.ipynb 실행
   ├─ Feature Engineering (Age, Area, Density, ...)
   ├─ 3개 ML Dataset 생성
   └─ 스케일링 & 라벨링

            ↓

4. ML 학습 & 해석
   ├─ aird_ml_learning_examples.ipynb 실행
   ├─ 모델 학습 (RF, XGBoost, ...)
   ├─ 성능 평가 (Accuracy, AUC, ...)
   └─ SHAP 해석

            ↓

5. 인사이트 도출
   ├─ 고위험 공장 리스트
   ├─ 위험 지역 순위
   ├─ 정책 권고사항
   └─ 시각화 (차트, 지도)

            ↓

6. 활용
   ├─ 정책: 예산 배분, 점검 우선순위
   └─ 민간: 보험, ESG 컨설팅, 입지 전략
```

---

## 💻 빠른 시작 (Quick Start)

### Step 1: 환경 설정

```bash
# Python 3.9 이상 필요
pip install pandas numpy matplotlib seaborn scikit-learn xgboost shap
```

### Step 2: 데이터 다운로드

1. 공공데이터포털에서 "전국 공장등록현황" 다운로드
2. 서울시 데이터만 필터링하여 `seoul_factory_registry_2025_v1.csv` 저장

### Step 3: 품질진단

```python
# aird_quality_diagnosis.ipynb 실행
jupyter notebook aird_quality_diagnosis.ipynb
```

### Step 4: ML Dataset 생성

```python
# aird_ml_factory_pack_builder.ipynb 실행
jupyter notebook aird_ml_factory_pack_builder.ipynb
```

### Step 5: ML 학습

```python
# aird_ml_learning_examples.ipynb 실행
jupyter notebook aird_ml_learning_examples.ipynb
```

### Step 6: 결과 확인

- `quality_report_YYYYMMDD_HHMMSS.html`: 품질 리포트
- `ml_factory_risk_seoul_2025_v1.csv`: 공장 리스크 데이터
- `ml_region_old_factory_share_seoul_2025_v1.csv`: 자치구 데이터
- `ml_location_score_candidate_sites_v1.csv`: 동 단위 데이터

---

## 🎓 학습 경로

### 초보자
1. ✅ **Starter Kit** 실행 (seoul_factory_hotspot_risk_starter_kit.ipynb)
2. 개요 문서 읽기 (본 문서)
3. 데이터 사전 확인 (02_data_dictionary.md)

### 중급자
1. 품질진단 노트북 실행
2. ML Pack 빌더 코드 분석
3. 데이터 사전 상세 학습

### 고급자
1. ML 학습 예제 전체 실행
2. SHAP 해석 분석
3. 커스텀 Feature 추가
4. 새로운 ML Task 설계

---

## 📊 제공 분석 예제

### 1. 고위험 공장 분류
- **모델**: Random Forest Classifier
- **Feature**: 연령, 면적, 밀집도, 동별 공장수
- **성능**: Accuracy ~85%, AUC ~0.90

### 2. 자치구 노후비중 예측
- **모델**: Logistic Regression, Random Forest
- **Feature**: 평균 연령, 공장 수, 동 수
- **활용**: 정책 우선순위 지역 선정

### 3. 동 단위 Hotspot 탐지
- **모델**: Random Forest Classifier
- **Feature**: 공장 수, 평균 연령, 노후비중
- **활용**: 산업입지 전략, 인프라 투자

---

## 🌐 확장 가능성

### 지역 확장
- **현재**: 서울시 (11,227개 공장)
- **확장**: 수도권 → 전국
- **방법**: 동일한 빌더 코드로 지역 필터만 변경

### 데이터 확장
- **추가 가능 데이터**:
  - 안전사고 이력
  - 환경 배출 데이터
  - 고용 정보
  - 경제지표
- **결합 방법**: 공장관리번호 기준 JOIN

### Task 확장
- **현재 Task**: 리스크 분류, 노후비중 분석, Hotspot 탐지
- **추가 가능 Task**:
  - 사고 발생 예측
  - 폐업 예측
  - 성장성 분석
  - 투자 효과 예측

---

## 🔐 데이터 사용 시 주의사항

### 개인정보 보호
- 회사명, 주소는 개인정보에 해당
- 공개 시 익명화 또는 집계 수준 권장

### 출처 표시
- 출처: 공공데이터포털 "전국 공장등록현황"
- 공공누리 라이선스 확인 필요

### 분석 한계
- 좌표 정보 없음 (지오코딩 필요)
- 실제 사고 이력 없음 (추가 데이터 결합 필요)

---

## 📚 관련 문서

- [데이터 사전](02_data_dictionary.md)
- [품질진단 가이드](03_quality_report.md)
- [ML 방법론](04_ml_methodology.md)
- [활용 사례](05_use_cases.md)
- [지도 시각화 가이드](aird_map_visualization_guide.md)

---

## 🤝 기여 방법

### 버그 리포트
- GitHub Issues 활용
- 재현 가능한 예제 포함

### 기능 제안
- Feature Request 템플릿 사용
- 활용 사례와 함께 제안

### 코드 기여
- Pull Request 환영
- 코드 스타일 가이드 준수

---

## 📞 문의 및 지원

- **이메일**: aird-support@example.com
- **GitHub**: https://github.com/your-org/aird-pack
- **문서**: https://aird-pack.readthedocs.io

---

## 📝 버전 정보

- **현재 버전**: 1.0
- **최종 업데이트**: 2025-11-28
- **호환성**: Python 3.9+, pandas 2.0+, scikit-learn 1.3+

---

## 🏆 주요 개선사항 (v1.0)

### 새로 추가된 기능
✅ 품질진단 노트북 (aird_quality_diagnosis.ipynb)  
✅ 5대 품질 차원 평가 시스템  
✅ HTML 품질 리포트 자동 생성  
✅ 지도 시각화 가이드  
✅ 체계적인 문서 구조  

### 개선된 부분
- Feature Engineering 로직 명확화
- 코드 주석 및 설명 강화
- 에러 처리 개선

---

**AIRD Pack - AI-Ready Data for Everyone** 🚀

*정부, 연구자, 민간이 함께 만드는 AI Ready Data 생태계*

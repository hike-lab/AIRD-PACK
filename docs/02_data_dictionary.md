# AIRD ML Pack 데이터 사전 (Data Dictionary)

## 문서 정보
- **버전**: 1.0
- **작성일**: 2025-11-28
- **대상**: 서울시 공장등록 AIRD ML Pack

---

## 1. 원본 데이터셋

### seoul_factory_registry_2025_v1.csv

서울시 공장등록 원본 데이터 (전국 공장등록현황 중 서울 추출)

| 컬럼명 | 데이터 타입 | 설명 | 예시 |
|--------|------------|------|------|
| index | int | 행 인덱스 | 0, 1, 2, ... |
| 시도명 | string | 시도 명칭 | 서울특별시 |
| 시군구명 | string | 시군구 명칭 | 종로구, 강남구 |
| 관리기관 | string | 공장 관리 기관 | 서울특별시 종로구 |
| 회사명 | string | 공장 운영 회사명 | (주)에취.알.디 |
| 공장구분 | string | 공장 유형 | 개별입지, 산업단지 |
| 단지명 | string | 산업단지 명칭 | (개별입지는 공백) |
| 설립구분 | string | 설립 유형 | 일반, 외국인투자 |
| 입주형태 | string | 입주 형태 | 해당없음, 임차 |
| 보유구분 | string | 소유 형태 | 임대, 자가 |
| 최초승인일 | string(YYYYMMDD) | 공장 최초 승인일 | 19980101 |
| 최초등록일 | string(YYYYMMDD) | 공장 최초 등록일 | 19940307 |
| 등록구분 | string | 등록 유형 | 등록변경, 신규등록 |
| 등록일 | string(YYYYMMDD) | 최근 등록일 | 19990510 |
| 전화번호 | string | 공장 전화번호 | 02-722-2001 |
| 남자종업원 | int | 남자 종업원 수 | 5 |
| 여자종업원 | int | 여자 종업원 수 | 2 |
| 외국인남자종업원 | int | 외국인 남자 종업원 수 | 0 |
| 외국인여자종업원 | int | 외국인 여자 종업원 수 | 0 |
| 종업원합계 | int | 전체 종업원 수 | 7 |
| 생산품 | string | 주요 생산품 | 모형전시물, IC TESTER |
| 원자재 | string | 주요 원자재 | 파워케이스, 저항박스 |
| 공장규모 | string | 공장 규모 분류 | 소기업, 중기업 |
| 용도지역 | string | 토지 용도지역 | 도시지역/주거지역 |
| 지목 | string | 지목 분류 | 대, 공장용지 |
| 용지면적 | float | 용지 면적 (㎡) | 157.55 |
| 제조시설면적 | float | 제조시설 면적 (㎡) | 79.11 |
| 부대시설면적 | float | 부대시설 면적 (㎡) | 78.44 |
| 건축면적 | float | 건축 면적 (㎡) | 157.55 |
| 지식산업센터명 | string | 지식산업센터 명칭 | (해당시) |
| 대표업종 | string | 대표 업종 | 33932 |
| 업종명 | string | 업종 명칭 | 전시용 모형 제조업 |
| 업종코드 | string | 한국표준산업분류 코드 | 33932, 18111 (복수) |
| 차수 | int | 차수 | 11 |
| 법인주소 | string | 법인 주소 | 서울특별시... |
| 필지수 | int | 필지 수 | 1 |
| 공장주소 | string | 공장 도로명 주소 | 서울특별시 종로구... |
| 공장주소_지번 | string | 공장 지번 주소 | 서울특별시 종로구 통의동... |
| 공장관리번호 | string | 고유 공장 관리번호 | 111100000000003 |

**총 레코드 수**: 11,227개  
**데이터 기준일**: 2025년 10월 말

---

## 2. ML Dataset 1: 공장 리스크 예측용

### ml_factory_risk_seoul_2025_v1.csv

공장 단위 고위험/저위험 분류를 위한 ML Dataset

#### 기본 정보
- **관측 단위**: 공장 (Factory)
- **레코드 수**: 5,521개
- **Target**: label_high_risk (Binary: 0/1)
- **활용 Task**: 고위험 공장 분류, 보험 리스크 평가

#### 컬럼 상세

| 컬럼명 | 타입 | 범주 | 설명 | 범위/예시 |
|--------|------|------|------|----------|
| factory_id | string | ID | 공장 관리번호 | 111100000000003 |
| company_name | string | ID | 회사명 | (주)에취.알.디 |
| region_gu | string | ID | 자치구 | 종로구, 강남구 |
| region_dong | string | ID | 동 | 통의동, 역삼동 |
| feature_base_age | float | Base | 공장 연령 (년) | 0~59 |
| feature_base_area | float | Base | 제조시설 면적 (㎡) | 4.5~14,863 |
| feature_base_area_log | float | Base | 제조시설 면적 로그 변환 | 1.5~9.6 |
| feature_base_gu_density | float | Base | 자치구 밀집도 점수 (0~1) | 0~1 |
| feature_agg_dong_factory_count | int | Aggregation | 동별 공장 수 | 1~1,132 |
| risk_score | float | Derived | 종합 리스크 점수 (0~1) | 0~1 |
| label_high_risk | int | Label | 고위험 여부 (0: 저위험, 1: 고위험) | 0, 1 |

#### Feature 생성 로직

**feature_base_age**: 
```
공장 연령 = 2025 - 최초승인일 년도
```

**feature_base_area_log**:
```
log(제조시설면적 + 1)  # 로그 변환으로 스케일 정규화
```

**feature_base_gu_density**:
```
자치구 밀집도 = 해당 구의 공장 수 / 전체 서울시 공장 수
MinMaxScaler로 0~1 정규화
```

**risk_score**:
```
위험 점수 = 0.4 * 정규화(공장연령) + 
            0.3 * 정규화(log(면적)) + 
            0.3 * 자치구_밀집도
```

**label_high_risk**:
```
상위 25% risk_score → 1 (고위험)
나머지 → 0 (저위험)
```

#### 활용 예시

```python
from sklearn.ensemble import RandomForestClassifier

# Feature와 Label 분리
feature_cols = [
    'feature_base_age',
    'feature_base_area_log',
    'feature_base_gu_density',
    'feature_agg_dong_factory_count'
]

X = df[feature_cols]
y = df['label_high_risk']

# 모델 학습
model = RandomForestClassifier(random_state=42)
model.fit(X_train, y_train)
```

---

## 3. ML Dataset 2: 자치구 노후공장 비중 분석용

### ml_region_old_factory_share_seoul_2025_v1.csv

자치구 단위 노후공장 비중 분석 및 예측용 ML Dataset

#### 기본 정보
- **관측 단위**: 자치구 (Region/Gu)
- **레코드 수**: 26개 (서울시 25개 구 + 기타)
- **Target**: label_high_old_share (Binary: 0/1)
- **활용 Task**: 노후공장 집중 지역 식별, 정책 우선순위

#### 컬럼 상세

| 컬럼명 | 타입 | 범주 | 설명 | 범위 |
|--------|------|------|------|------|
| region_gu | string | ID | 자치구명 | 강남구, 강동구, ... |
| feature_avg_age | float | Aggregation | 평균 공장 연령 (년) | 20.6~27.0 |
| feature_old_factory_count | int | Aggregation | 노후공장 수 (≥20년) | 6~151 |
| feature_factory_count | int | Aggregation | 전체 공장 수 | 75~682 |
| feature_avg_area_log | float | Aggregation | 평균 면적 로그값 | 4.3~4.7 |
| feature_old_share | float | Derived | 노후공장 비중 (0~1) | 0.03~0.27 |
| feature_dong_count | int | Aggregation | 해당 구의 동 수 | 21~148 |
| label_high_old_share | int | Label | 노후비중 高 여부 (0/1) | 0, 1 |

#### Feature 생성 로직

**feature_old_share**:
```
노후공장 비중 = 노후공장 수 / 전체 공장 수
(노후공장: 공장 연령 ≥ 20년)
```

**label_high_old_share**:
```
상위 50% feature_old_share → 1
하위 50% → 0
```

#### 활용 예시

```python
# 분류 모델
X = df[['feature_avg_age', 'feature_factory_count', 'feature_dong_count']]
y = df['label_high_old_share']

# 회귀 모델 (노후 비중 직접 예측)
y_reg = df['feature_old_share']
```

---

## 4. ML Dataset 3: 동 단위 Hotspot 분석용

### ml_location_score_candidate_sites_v1.csv

동 단위 제조업 Hotspot 탐지 및 입지 스코어링용 ML Dataset

#### 기본 정보
- **관측 단위**: 동 (Dong)
- **레코드 수**: 1,858개
- **Target**: label_hotspot (Binary: 0/1)
- **활용 Task**: 제조업 집적지 발굴, 산업입지 전략

#### 컬럼 상세

| 컬럼명 | 타입 | 범주 | 설명 | 범위 |
|--------|------|------|------|------|
| region_gu | string | ID | 자치구명 | 강남구, 강동구, ... |
| region_dong | string | ID | 동명 | 역삼동, 통의동, ... |
| feature_avg_age | float | Aggregation | 동별 평균 공장 연령 | 0~59 |
| feature_old_share | float | Aggregation | 동별 노후공장 비중 | 0~1 |
| feature_factory_count | int | Aggregation | 동별 공장 수 | 1~1,132 |
| feature_avg_area_log | float | Aggregation | 동별 평균 면적 로그 | 1.5~9.6 |
| feature_gu_density | float | Base | 자치구 밀집도 점수 | 0~1 |
| label_hotspot | int | Label | Hotspot 여부 (0/1) | 0, 1 |

#### Feature 생성 로직

**label_hotspot**:
```
상위 25% feature_factory_count → 1 (Hotspot)
나머지 → 0
```

#### 활용 예시

```python
# Hotspot 분류
X = df[['feature_avg_age', 'feature_old_share', 
        'feature_factory_count', 'feature_gu_density']]
y = df['label_hotspot']

# Hotspot 확률 예측
model.predict_proba(X)[:, 1]  # Hotspot일 확률
```

---

## 5. 데이터 품질 지표

### 완전성 (Completeness)
- 원본 데이터 결측치: 일부 컬럼에서 10~30% 결측
- ML Dataset: 필수 Feature만 포함, 결측치 제거 완료

### 정확성 (Accuracy)
- 날짜 형식: YYYYMMDD 표준 준수
- 주소 형식: 서울특별시 시작 검증 완료

### 일관성 (Consistency)
- 중복 레코드: 공장관리번호 기준 중복 제거
- 범주형 값: 표준화 완료

### 적시성 (Timeliness)
- 데이터 기준일: 2025년 10월 말
- 최신 공장 승인: 2025년까지 포함

### 유효성 (Validity)
- 수치 범위: 음수 값 제거
- 논리적 검증: 면적 대소관계 확인

---

## 6. 데이터 사용 시 주의사항

### 개인정보 보호
- 회사명, 주소는 개인정보에 해당할 수 있음
- 공개 시 익명화 또는 집계 수준으로 제공 권장

### 분석 제약
- 좌표 정보 없음: 지도 시각화 시 지오코딩 필요
- 사고 이력 없음: 실제 리스크는 추가 데이터 결합 필요

### 라이선스
- 출처: 공공데이터포털 (전국 공장등록현황)
- 이용조건: 출처 표시, 공공누리 유형 확인 필요

---

## 7. 변경 이력

| 버전 | 날짜 | 변경 내용 |
|------|------|----------|
| 1.0 | 2025-11-28 | 초기 버전 작성 |

---

## 8. 참고 문서

- [AIRD Pack 개요](01_overview.md)
- [품질진단 리포트](03_quality_report.md)
- [ML 방법론 설명](04_ml_methodology.md)
- [활용 사례집](05_use_cases.md)

---

**문서 관리자**: AIRD Pack 개발팀  
**문의**: aird-support@example.com

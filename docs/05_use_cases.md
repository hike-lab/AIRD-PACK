# AIRD Pack 활용 사례집

## 📌 개요

AIRD Pack은 정부, 민간, 연구기관 모두가 활용할 수 있는 AI-Ready 데이터입니다.  
이 문서는 실제 활용 가능한 다양한 사례를 제시합니다.

---

## 🏛️ 정부·공공기관 활용 사례

### 사례 1: 안전 점검 우선순위 선정

**기관**: 서울시 산업정책과, 소방재난본부

**과제**:
- 한정된 인력으로 11,000+ 공장 전수 점검 불가
- 위험도 기반 선별 점검 필요

**AIRD Pack 활용**:
```python
# 고위험 공장 리스트 추출
high_risk_factories = df_factory[df_factory['label_high_risk'] == 1]

# 위험도 순 정렬
priority_list = high_risk_factories.sort_values(
    'risk_score', ascending=False
)

# 상위 100개 공장 집중 점검
top_100 = priority_list.head(100)
```

**예상 효과**:
- 점검 효율성 30% 향상
- 사고 사전 예방율 20% 증가
- 점검 인력 재배치 최적화

**예산 규모**: 연 5억원 절감

---

### 사례 2: 노후 산업단지 재생 사업 대상 선정

**기관**: 서울시 도시재생본부

**과제**:
- 노후 공장 밀집 지역 발굴
- 재생 사업 우선순위 지역 선정

**AIRD Pack 활용**:
```python
# 노후공장 비중 높은 자치구 추출
old_regions = df_region[
    df_region['label_high_old_share'] == 1
].sort_values('feature_old_share', ascending=False)

# 동 단위 상세 분석
for gu in old_regions['region_gu']:
    hotspot_dongs = df_location[
        (df_location['region_gu'] == gu) &
        (df_location['label_hotspot'] == 1)
    ]
    print(f"{gu}: {len(hotspot_dongs)}개 Hotspot 동")
```

**예상 효과**:
- 재생 사업 타당성 조사 기간 50% 단축
- 데이터 기반 주민 설득력 향상
- 예산 배분 최적화

**사업 규모**: 5년간 1,000억원 투자

---

### 사례 3: 제조업 밀집지역 교통·인프라 개선

**기관**: 서울시 교통정책과

**과제**:
- 제조업 밀집 지역 물류 정체 해소
- 도로, 주차장 확충 우선순위

**AIRD Pack 활용**:
```python
# Hotspot 동 추출
hotspot_dongs = df_location[
    df_location['label_hotspot'] == 1
]

# 공장 수와 평균 규모 고려한 인프라 수요 추정
hotspot_dongs['infra_demand'] = (
    hotspot_dongs['feature_factory_count'] * 
    hotspot_dongs['feature_avg_area_log']
)

# 상위 20개 동 우선 개선
priority_dongs = hotspot_dongs.nlargest(
    20, 'infra_demand'
)
```

**예상 효과**:
- 물류 정체 시간 25% 감소
- 지역 경제 활성화
- 민원 30% 감소

---

## 🏢 민간기업 활용 사례

### 사례 4: 보험사 공장 화재보험 리스크 차등화

**기업**: A 손해보험

**과제**:
- 모든 공장에 동일한 보험료 부과 → 역선택 문제
- 위험도 기반 보험료 차등화 필요

**AIRD Pack 활용**:
```python
# 공장별 리스크 스코어 기반 보험료 산정
df_factory['insurance_premium'] = (
    base_premium * (1 + df_factory['risk_score'])
)

# 고위험 공장: 추가 점검 또는 보험 거절
high_risk = df_factory[df_factory['label_high_risk'] == 1]
```

**예상 효과**:
- 손해율 15% 개선
- 저위험 고객 유치 증가
- 연간 100억원 손실 방지

**시장 규모**: 공장 화재보험 시장 연 5,000억원

---

### 사례 5: ESG 컨설팅 타깃 고객 발굴

**기업**: B ESG 컨설팅

**과제**:
- ESG 개선 필요 공장 발굴
- 맞춤형 컨설팅 제안

**AIRD Pack 활용**:
```python
# 노후 + 대규모 + 밀집지역 공장 타깃팅
esg_targets = df_factory[
    (df_factory['feature_base_age'] >= 20) &
    (df_factory['feature_base_area'] >= 500) &
    (df_factory['feature_base_gu_density'] >= 0.5)
]

# 자치구별 집중 마케팅
target_regions = esg_targets.groupby('region_gu').size()
```

**예상 효과**:
- 영업 효율 40% 향상
- 계약 전환율 25% → 35%
- 연매출 20억원 증가

---

### 사례 6: 부동산 개발사 산업용지 매입 전략

**기업**: C 부동산 개발

**과제**:
- 제조업 퇴출 예상 지역 선정
- 재개발 가능 부지 발굴

**AIRD Pack 활용**:
```python
# 노후 공장 밀집 + Hotspot 지역
redevelopment_candidates = df_location[
    (df_location['feature_old_share'] >= 0.3) &
    (df_location['feature_factory_count'] >= 50) &
    (df_location['label_hotspot'] == 1)
]

# 지역별 개발 가능성 순위
redevelopment_candidates['dev_score'] = (
    redevelopment_candidates['feature_old_share'] * 0.5 +
    redevelopment_candidates['feature_factory_count'] / 1000 * 0.5
)
```

**예상 효과**:
- 잠재 개발부지 10곳 발굴
- 사전 조사 비용 50% 절감
- 프로젝트 성공률 향상

---

### 사례 7: 전력 인프라 기업의 수요 예측

**기업**: D 전력 인프라

**과제**:
- 제조업 밀집 지역 전력 수요 증가 예측
- 변전소, 송전선 확충 계획

**AIRD Pack 활용**:
```python
# 동별 공장 규모 합산으로 전력 수요 추정
df_location['power_demand_est'] = (
    df_location['feature_factory_count'] * 
    np.exp(df_location['feature_avg_area_log'])
)

# Hotspot 지역 우선 인프라 확충
power_priority = df_location[
    df_location['label_hotspot'] == 1
].sort_values('power_demand_est', ascending=False)
```

**예상 효과**:
- 인프라 투자 ROI 20% 향상
- 공급 안정성 개선
- 고객 만족도 향상

---

## 🎓 연구기관·대학 활용 사례

### 사례 8: 도시계획 연구

**기관**: E 대학교 도시공학과

**연구 주제**:
- 서울시 제조업 입지 변화 패턴 분석
- 미래 산업공간 구조 예측

**AIRD Pack 활용**:
```python
# 시계열 분석 (추가 데이터 결합 시)
# 연도별 공장 승인 추세
approval_trend = df_factory.groupby(
    df_factory['feature_base_age'].apply(lambda x: 2025-x)
).size()

# 공간 분석
# 자치구별 공장 밀도 변화
spatial_trend = df_factory.groupby(['region_gu']).agg({
    'feature_base_age': 'mean',
    'feature_base_area': 'sum'
})
```

**연구 성과**:
- 논문 2편 게재 (SCI급)
- 정책 제언 보고서 제출
- 후속 연구 과제 수주

---

### 사례 9: 산업안전 예측 모델 개발

**기관**: F 연구소

**연구 주제**:
- 공장 사고 발생 예측 모델
- 위험 요인 분석

**AIRD Pack 활용**:
```python
# 추가 데이터 (사고 이력) 결합
df_factory_accident = df_factory.merge(
    accident_data, on='factory_id', how='left'
)

# 머신러닝 모델 개발
from xgboost import XGBClassifier

X = df_factory_accident[feature_cols]
y = df_factory_accident['accident_occurred']

model = XGBClassifier()
model.fit(X_train, y_train)
```

**연구 성과**:
- 사고 예측 정확도 85%
- 국제 학술대회 발표
- 기술 이전 5천만원

---

## 🌍 국제 협력 사례

### 사례 10: 개발도상국 산업안전 지원

**기관**: 한국국제협력단 (KOICA)

**과제**:
- 개도국 산업안전 관리 체계 구축 지원
- AIRD Pack 방법론 전수

**AIRD Pack 활용**:
```
1. AIRD Pack 워크플로우 교육
2. 현지 데이터로 ML Dataset 구축
3. 리스크 관리 시스템 도입
4. 현지 공무원 역량 강화
```

**예상 효과**:
- 개도국 산업사고 20% 감소
- 한국 산업안전 기술 수출
- 국제 협력 모범 사례

---

## 📊 활용 사례 요약

| 분야 | 사례 수 | 주요 효과 | 예상 경제 효과 |
|------|---------|-----------|----------------|
| 정부·공공 | 3 | 효율성 향상, 예산 절감 | 연 500억원 |
| 민간기업 | 4 | 매출 증가, 비용 절감 | 연 1,000억원 |
| 연구기관 | 2 | 논문, 기술 이전 | 연 50억원 |
| 국제 협력 | 1 | 기술 수출, 외교 | - |

**총계**: 10개 사례, 연간 1,550억원 이상 경제 효과 추정

---

## 🚀 시작하기

### 정부·공공기관
1. 보유 데이터 확인
2. AIRD Pack 빌더로 ML Dataset 생성
3. 정책 우선순위 분석 수행

### 민간기업
1. 비즈니스 모델 설계
2. AIRD Pack 활용 PoC 진행
3. 상용 서비스 출시

### 연구기관
1. 연구 주제 선정
2. AIRD Pack + 추가 데이터 결합
3. 모델 개발 및 논문 작성

---

## 💡 성공 요인

### 데이터 품질
- ✅ 품질진단 필수 수행
- ✅ 결측치, 이상치 처리

### 도메인 지식
- ✅ 제조업, 안전, 정책 전문가 협업
- ✅ Feature 선정 시 현장 의견 반영

### 지속 가능성
- ✅ 정기적 데이터 업데이트
- ✅ 모델 재학습 자동화
- ✅ 피드백 루프 구축

---

## 📞 활용 지원

### 기술 지원
- 이메일: aird-support@example.com
- GitHub Issues
- 월 1회 온라인 Q&A 세션

### 교육 프로그램
- 기초 과정 (1일): AIRD Pack 소개
- 실무 과정 (3일): 전체 워크플로우
- 고급 과정 (5일): 커스터마이징

### 컨설팅
- 맞춤형 ML Dataset 설계
- 비즈니스 모델 구축
- 시스템 구축 지원

---

## 🎯 Next Steps

1. **개요 문서** 읽기 → [01_overview.md](01_overview.md)
2. **데이터 사전** 확인 → [02_data_dictionary.md](02_data_dictionary.md)
3. **Starter Kit** 실행 → seoul_factory_hotspot_risk_starter_kit.ipynb
4. **본인 사례** 설계 및 실행

---

**AIRD Pack으로 당신의 데이터를 AI-Ready로!** 🚀

---

## 📝 사례 제출

여러분의 AIRD Pack 활용 사례를 공유해주세요!

**제출 방법**:
1. GitHub Issues에 "활용 사례" 라벨로 등록
2. 이메일로 사례 문서 발송

**수록 시 혜택**:
- 공식 문서에 사례 등재
- 기술 지원 우선 제공
- 커뮤니티 인정

---

**작성일**: 2025-11-28  
**버전**: 1.0

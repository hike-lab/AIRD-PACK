# AIRD 지도 시각화 가이드

이 문서는 AIRD ML Pack의 결과를 지도 위에 시각화하는 방법을 안내합니다.

## 필수 라이브러리

```bash
pip install folium geopandas plotly
```

## 1. Folium을 사용한 기본 지도 시각화

```python
import pandas as pd
import folium
from folium import plugins

# ML Dataset 로드
df_factory = pd.read_csv('ml_factory_risk_seoul_2025_v1.csv')
df_location = pd.read_csv('ml_location_score_candidate_sites_v1.csv')

# 서울 중심 좌표
seoul_center = [37.5665, 126.9780]

# 기본 지도 생성
m = folium.Map(
    location=seoul_center,
    zoom_start=11,
    tiles='OpenStreetMap'
)

# 자치구별 데이터 집계
gu_risk = df_factory.groupby('region_gu').agg({
    'label_high_risk': 'mean',
    'risk_score': 'mean'
}).reset_index()

# 서울시 자치구 경계 데이터 (GeoJSON) 필요
# 공공데이터포털에서 다운로드 가능

# 지도 저장
m.save('seoul_factory_risk_map.html')
print("✅ 지도 저장 완료: seoul_factory_risk_map.html")
```

## 2. Hotspot 히트맵

```python
import folium.plugins as plugins

# Hotspot 데이터 준비
hotspots = df_location[df_location['label_hotspot'] == 1]

# 히트맵 생성 (가상 좌표 사용 예시)
# 실제로는 주소 → 좌표 변환 필요
heat_data = []
for idx, row in hotspots.iterrows():
    # 좌표가 있다고 가정
    if 'latitude' in hotspots.columns and 'longitude' in hotspots.columns:
        heat_data.append([row['latitude'], row['longitude'], row['feature_factory_count']])

if heat_data:
    m = folium.Map(location=seoul_center, zoom_start=11)
    plugins.HeatMap(heat_data).add_to(m)
    m.save('seoul_factory_heatmap.html')
    print("✅ 히트맵 저장 완료")
```

## 3. 자치구별 리스크 코로플레스 맵

```python
# GeoJSON 데이터와 결합
# 서울시 자치구 경계 데이터 필요

import folium

m = folium.Map(location=seoul_center, zoom_start=11)

# 코로플레스 맵 (Choropleth)
folium.Choropleth(
    geo_data='seoul_districts.geojson',  # 자치구 경계 GeoJSON
    name='choropleth',
    data=gu_risk,
    columns=['region_gu', 'risk_score'],
    key_on='feature.properties.name',
    fill_color='YlOrRd',
    fill_opacity=0.7,
    line_opacity=0.2,
    legend_name='평균 리스크 점수'
).add_to(m)

folium.LayerControl().add_to(m)
m.save('seoul_risk_choropleth.html')
```

## 4. Plotly를 사용한 인터랙티브 시각화

```python
import plotly.express as px
import plotly.graph_objects as go

# 자치구별 통계
gu_stats = df_factory.groupby('region_gu').agg({
    'feature_base_age': 'mean',
    'feature_base_area': 'mean',
    'label_high_risk': 'sum'
}).reset_index()

# 버블 차트
fig = px.scatter(
    gu_stats,
    x='feature_base_age',
    y='feature_base_area',
    size='label_high_risk',
    color='region_gu',
    hover_name='region_gu',
    title='서울시 자치구별 공장 프로파일',
    labels={
        'feature_base_age': '평균 공장 연령',
        'feature_base_area': '평균 제조시설 면적',
        'label_high_risk': '고위험 공장 수'
    }
)

fig.write_html('factory_profile_bubble.html')
print("✅ 인터랙티브 차트 저장 완료")
```

## 5. 주소 → 좌표 변환

공장 주소를 위도/경도로 변환하려면 지오코딩 API가 필요합니다.

### 카카오 로컬 API 사용 예시

```python
import requests

def get_coordinates(address, api_key):
    """
    주소를 위도/경도로 변환
    """
    url = "https://dapi.kakao.com/v2/local/search/address.json"
    headers = {"Authorization": f"KakaoAK {api_key}"}
    params = {"query": address}
    
    try:
        response = requests.get(url, headers=headers, params=params)
        result = response.json()
        
        if result['documents']:
            return {
                'latitude': float(result['documents'][0]['y']),
                'longitude': float(result['documents'][0]['x'])
            }
    except:
        pass
    
    return None

# 사용 예시
# api_key = 'YOUR_KAKAO_API_KEY'
# coords = get_coordinates('서울특별시 종로구 통의동 35-69', api_key)
```

### Google Geocoding API 사용

```python
from geopy.geocoders import GoogleV3

def geocode_address(address):
    """
    Google Geocoding API 사용
    """
    geolocator = GoogleV3(api_key='YOUR_GOOGLE_API_KEY')
    try:
        location = geolocator.geocode(address)
        if location:
            return {
                'latitude': location.latitude,
                'longitude': location.longitude
            }
    except:
        pass
    
    return None
```

## 6. 전체 워크플로우 예시

```python
import pandas as pd
import folium
from folium import plugins
import plotly.express as px

# 1. 데이터 로드
df_factory = pd.read_csv('ml_factory_risk_seoul_2025_v1.csv')
df_location = pd.read_csv('ml_location_score_candidate_sites_v1.csv')

# 2. 기본 통계
print("=" * 80)
print("📊 데이터 개요")
print("=" * 80)
print(f"전체 공장 수: {len(df_factory):,}개")
print(f"고위험 공장 수: {df_factory['label_high_risk'].sum():,}개")
print(f"Hotspot 동: {df_location['label_hotspot'].sum()}개")

# 3. 자치구별 집계
gu_summary = df_factory.groupby('region_gu').agg({
    'factory_id': 'count',
    'label_high_risk': 'sum',
    'risk_score': 'mean',
    'feature_base_age': 'mean'
}).reset_index()

gu_summary.columns = ['자치구', '공장수', '고위험공장수', '평균리스크', '평균연령']
gu_summary = gu_summary.sort_values('평균리스크', ascending=False)

print("\\n📈 자치구별 리스크 Top 10")
print(gu_summary.head(10).to_string(index=False))

# 4. 시각화
# 4-1. 막대 차트
fig1 = px.bar(
    gu_summary.head(10),
    x='자치구',
    y='평균리스크',
    color='고위험공장수',
    title='자치구별 평균 리스크 점수 Top 10',
    labels={'평균리스크': '평균 리스크 점수', '고위험공장수': '고위험 공장 수'}
)
fig1.write_html('gu_risk_ranking.html')

# 4-2. 지도 (좌표가 있는 경우)
# m = folium.Map(location=[37.5665, 126.9780], zoom_start=11)
# ... 지도 작성 코드 ...
# m.save('seoul_risk_map.html')

print("\\n✅ 모든 시각화 완료")
```

## 7. 주의사항

### 개인정보 보호
- 공장 주소는 개인정보일 수 있으므로, 공개 시 주의 필요
- 동/구 수준으로 집계하여 시각화 권장

### 지오코딩 비용
- 대량의 주소를 변환할 경우 API 비용 발생
- 배치 처리 및 캐싱 권장

### GeoJSON 데이터
- 서울시 행정구역 경계 데이터는 공공데이터포털에서 다운로드
- 또는 GitHub에서 오픈소스 데이터 활용

## 8. 참고 자료

- **Folium 공식 문서**: https://python-visualization.github.io/folium/
- **Plotly 공식 문서**: https://plotly.com/python/
- **서울시 오픈데이터**: https://data.seoul.go.kr/
- **공공데이터포털**: https://www.data.go.kr/

## 9. 다음 단계

지도 시각화를 완성한 후:
1. Streamlit/Dash를 사용하여 인터랙티브 대시보드 구축
2. 정기적으로 업데이트되는 모니터링 시스템 구축
3. 리스크 알림 시스템 연동

---

**AIRD 지도 시각화 가이드 v1.0**  
**작성일:** 2025-11-28

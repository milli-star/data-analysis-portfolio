# Smart Logistics 배송 지연 분석

## 분석 목적
물류 배송 과정에서 발생하는 지연(Delay)이 어떤 조건에서 발생하는지 탐색적으로 파악하여, 실무적으로 개선 가능한 지점을 찾는다.

## 데이터 개요
- 출처: [Kaggle - Smart Logistics Supply Chain Dataset](https://www.kaggle.com/datasets/ziya07/smart-logistics-supply-chain-dataset)
- 규모: 1,000행 × 16열
- 기간: 2024-01-01 ~ 2024-12-30 (1년치)
- 전체 지연 비율: 56.6% (566건 지연 / 1,000건)

## 주요 발견

### 1. 교통 상황(Traffic_Status)이 지연을 결정짓는 유일한 요인
| 교통 상황 | 전체건수 | 지연건수 | 지연비율 |
|---|---|---|---|
| Clear (원활) | 328 | 115 | 35.1% |
| Detour (우회) | 345 | 124 | 35.9% |
| **Heavy (정체)** | 327 | 327 | **100.0%** |

교통이 Heavy(정체) 상태이면 예외 없이 전부 지연으로 이어졌다. 반면 Clear/Detour는 지연 여부에 큰 차이를 만들지 않았다.

### 2. 지연 "사유" 라벨과 실제 데이터가 불일치
지연 사유는 Weather(267건), Traffic(236건), Mechanical Failure(234건)로 고르게 기록되어 있었으나, 실제 관련 수치 데이터와 대조한 결과:

| 사유 | 관련 수치 컬럼 | 상관계수 | 실제 연관성 |
|---|---|---|---|
| Traffic | Traffic_Status | - | 강한 연관 (검증됨) |
| Weather | Temperature, Humidity | -0.038 / -0.001 | 연관 없음 |
| Mechanical Failure | Asset_Utilization | -0.003 | 연관 없음 |

Weather, Mechanical Failure로 기록된 지연 사유는 실제 온도·습도·자산가동률과 통계적으로 무관했다. Traffic만 실제 데이터(Traffic_Status)와 일치했다.

### 3. 그 외 요인들은 지연과 무관
시간대, 요일, 평일/주말, 재고 수준, 트럭(자산)별, 수요 예측 — 총 8개 요인을 상관계수와 구간별 비교로 검증했으나 모두 유의미한 관계를 보이지 않았다 (상관계수 전부 ±0.04 이내).

## 시사점
- 지연 예방을 위한 실무적 대응은 **교통 상황(특히 Heavy 구간) 사전 예측 및 우회 경로 확보**에 집중하는 것이 가장 효과적으로 판단된다.
- 지연 사유 기록(Logistics_Delay_Reason)은 실제 원인 분석보다는 형식적 분류에 가까울 수 있어, 향후 데이터 수집 시 사유 기록 방식에 대한 재검토가 필요하다.

## 사용 기술
Python, pandas, matplotlib, seaborn

## 데이터 파일
`data/smart_logistics_dataset.csv` (Kaggle 원본, 재배포 가능 여부는 출처 라이선스 확인 필요)
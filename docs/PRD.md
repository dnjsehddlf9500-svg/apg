# APG Product Requirements Document

## 1. Product Overview
APG (AutoPlan for Golf)는 사용자의 조건을 기반으로
일본 전역의 직항 목적지를 탐색하고,
항공 + 골프 + 호텔 + 이동을 조합하여
최적의 일본 골프 자유여행 플랜을 추천한다.

## 2. Problem
일본 골프 자유여행을 계획하려면
항공, 골프장, 호텔, 위치, 이동시간 등을
여러 서비스에서 반복 검색해야 한다.

현재 이 과정에는 실제로 2~3일이 걸릴 수 있다.

## 3. Mission
Reduce 2–3 days of Japan golf trip planning to 5 minutes.

## 4. Core Principle
사용자는 목적지를 선택하지 않는다.
사용자는 조건을 선택한다.
APG가 목적지를 찾는다.

## 5. User Inputs
- 출발 공항
  - ICN
  - GMP
  - CJJ
  - TAE
  - PUS
- 여행 시기
  - 특정 날짜
  - 월 단위
- 여행 기간
- 인원
- 라운드 수
- 목표 예산
  - 기본 탐색 범위 ±30만원
- 주말 포함 / 제외
- 숙소 스타일
  - 도심
  - 골프장 인근
- 가격 ↔ 이동편의 선호도

## 6. APG Search Flow
사용자 조건 입력

→ 출발공항 기준 일본 직항 목적지 탐색

→ 목적지별 가능한 여행 일정 탐색

→ 항공편 조합

→ 골프장 및 티타임 조합

→ 호텔 조합

→ 이동거리 계산

→ 총 여행비용 계산

→ APG Score 계산

→ 최적 플랜 Ranking

## 7. Result
### APG Pick
- 목적지
- 여행 날짜
- 왕복 항공편
- 호텔
- 골프장 / 티타임
- 예상 이동시간
- 1인 총 예상비용
- 추천 이유

사용자는 각 예약처로 이동하여 직접 예약한다.

## 8. Booking
- Flight → 외부 예약처
- Golf → Rakuten GORA
- Hotel → Affiliate Partner
- Rental Car → 외부 예약처

APG는 초기 MVP에서 직접 결제/예약을 처리하지 않는다.

## 9. Performance
사용자는 조건을 반복적으로 변경할 것을 전제로 한다.

따라서:
- 직항 노선 데이터 캐싱
- 항공 데이터 캐싱
- 골프장 데이터 캐싱
- 호텔 데이터 캐싱
- 동일 검색 결과 재사용
- 변경된 조건만 재계산

을 기본 구조로 한다.

## 10. MVP Goal
한 명의 실제 사용자가

조건 입력
→ APG Pick 확인
→ 예약처 이동
→ 실제 일본 골프여행 예약

까지 완료할 수 있도록 한다.

## 11. Out of Scope
MVP에서는 하지 않는다.

- 자체 결제
- 자체 예약
- 커뮤니티
- 자체 리뷰
- 포인트
- 멤버십
- 여행상품 판매

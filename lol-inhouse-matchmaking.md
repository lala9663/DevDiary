# 🎮 LoL Inhouse Matchmaking Platform

## 📌 프로젝트 개요

친구들과 **LoL 내전(5 vs 5)**을 쉽게 진행할 수 있도록 하는 **자동 매칭 플랫폼**.

목표 기능

- Riot OAuth 로그인
- 내전 방 생성 / 참가
- 실시간 인원 확인
- 10명 모이면 자동 팀 매칭
- 매칭 결과 확인
- Discord 알림
- 경기 기록 저장
- Grafana 통계 분석

---

# 🎯 프로젝트 목표

초기 목표

- 친구들끼리 사용하는 **내전 매칭 사이트**

확장 목표

- 누구나 사용할 수 있는 **LoL 내전 플랫폼**

예시 기능

- Riot 계정 로그인
- 자동 팀 밸런싱
- 실시간 방 시스템
- 통계 분석
- Discord 연동

---

# 🧱 전체 아키텍처
User  
│  
▼  
Frontend (Next.js)  
│  
▼  
Backend API (FastAPI)  
│  
├ PostgreSQL (서비스 데이터)  
│  
├ Redis (실시간 방 관리)  
│  
├ Riot API  
│  
├ Discord Bot  
│  
└ Match Event  
│  
▼  
InfluxDB (TimeSeries)  
│  
▼  
Grafana

---

# 🧰 기술 스택

## Frontend

**Next.js (React)**

역할

- Riot 로그인 UI
- 방 생성 / 참가
- 실시간 방 상태
- 매칭 결과 화면
- 간단한 통계 표시

---

## Backend

**Python FastAPI**

역할

- Riot OAuth 처리
- Riot API 호출
- 매칭 알고리즘
- 방 관리
- WebSocket
- 데이터 저장

---

## Database

**PostgreSQL**

서비스 데이터 관리

---

## Cache / Real-time

**Redis**

사용 이유

- 방 상태 관리
- 실시간 참가 처리
- WebSocket 보조

---

## Analytics

**InfluxDB + Grafana**

용도

- 승률
- 조합 분석
- 라인 통계
- 활동량 분석

---

# 🔐 Riot OAuth 로그인

로그인 흐름
User  
↓  
Login with Riot  
↓  
Riot Authorization  
↓  
callback  
↓  
Access Token  
↓  
Account API  
↓  
User 저장


가져오는 정보

- puuid
- gameName
- tagLine
- profile icon

추가 조회

- tier
- rank

---

# 🗄 데이터 모델

## users
id  
puuid  
game_name  
tag_line  
tier  
rank  
profile_icon  
created_at

---

## rooms
id  
room_name  
owner_id  
status

waiting  
matching  
finished

created_at

---

## room_players
room_id  
user_id  
lane  
team  
joined_at

---

## matches
id  
room_id  
team_a_mmr  
team_b_mmr  
winner  
created_at

---

## match_players
match_id  
user_id  
team  
lane  
champion  
result

---

# ⚙ 핵심 기능 플로우

## 1️⃣ Riot 로그인

유저
Login with 

서버

- OAuth 인증
- 유저 저장

---

## 2️⃣ 로비

예시 화면
현재 방

금요일 내전 (7/10)  
주말 내전 (3/10)

[방 생성]

---

## 3️⃣ 방 생성

입력 정보

- 방 이름
- 티어 제한 (optional)
- 비밀번호 (optional)

---

## 4️⃣ 방 입장

예시
금요일 내전

현재 인원

1 HideOnBush (Diamond)  
2 Faker (Master)  
3 Dopa (Challenger)

3 / 10

---

## 5️⃣ 실시간 업데이트

WebSocket 이벤트
player_join  
player_leave  
match_start

---

## 6️⃣ 매칭 시작

조건
10명

버튼
Match Start
------
  
# 🧠 매칭 알고리즘  
  
## 티어 점수  

Challenger 100  
Grandmaster 95  
Master 90  
Diamond 80  
Platinum 70  
Gold 60  
Silver 50  
Bronze 40  
Iron 30

  
---  
  
## 매칭 과정  
  
1️⃣ 플레이어 정렬  

MMR 높은 순

  
2️⃣ 팀 분배  

Snake 방식

  
예시  

1 → TEAM A  
2 → TEAM B  
3 → TEAM B  
4 → TEAM A  
5 → TEAM A  
6 → TEAM B

  
목표  

TEAM A ≈ TEAM B

  
---  
  
# 🖥 매칭 결과 화면  

TEAM A

TOP  
JG  
MID  
ADC  
SUP

VS

TEAM B

TOP  
JG  
MID  
ADC  
SUP

  
---  
  
# 🤖 Discord 연동  
  
매칭 완료 시  
  
Discord Bot 메시지  

내전 매칭 완료

TEAM A  
TEAM B

사이트 링크

  
추가 기능  
  
- 자동 음성 채널 생성  
  
---  
  
# 📊 통계 시스템  
  
매치 종료 시  
  
이벤트 발생  

match_finished

  
서버 처리  
  
- match 저장  
- player_stats 업데이트  
- InfluxDB 기록  
  
---  
  
# 📈 Grafana 분석  
  
대시보드 예  
  
### 개인 승률  

Win Rate Over Time

  
---  
  
### 라인 승률  

TOP  
JG  
MID  
ADC  
SUP

  
---  
  
### Duo 승률  

A + B = 72%  
A + C = 48%

  
---  
  
### 활동량  

Matches per Day

  
---  
  
# 🚀 개발 단계  
  
## Phase 1 (MVP)  
  
- [ ] Riot OAuth 로그인  
- [ ] 유저 DB 저장  
- [ ] 방 생성  
- [ ] 방 참가  
  
---  
  
## Phase 2  
  
- [ ] WebSocket  
- [ ] 실시간 방 상태  
  
---  
  
## Phase 3  
  
- [ ] 자동 팀 매칭 알고리즘  
  
---  
  
## Phase 4  
  
- [ ] match 기록 저장  
  
---  
  
## Phase 5  
  
- [ ] Grafana 통계  
- [ ] Discord 연동  
  
---  
  
# 📌 향후 기능  
  
### 라인 선택  

TOP  
JG  
MID  
ADC  
SUP  
FILL

  
---  
  
### 챔피언 추천  
  
Riot API  

Champion Mastery

  
---  
  
### Best Duo  

같이 하면 승률 높은 사람

  
---  
  
### 팀 밸런스 점수  
  
예시  

TEAM A 2100  
TEAM B 2080

Balance Score  
98%

  
---  
  
# ⚠ 핵심 난이도 포인트  
  
이 프로젝트에서 중요한 3가지  
  
1️⃣ Riot OAuth    
2️⃣ WebSocket 방 관리    
3️⃣ 팀 밸런스 알고리즘    
  
이 3가지를 잘 설계하면 프로젝트 완성도가 크게 올라간다.  
  
---  
  
# 📂 추천 레포 구조  

lol-inhouse-platform

frontend/  
nextjs

backend/  
fastapi

infra/  
docker  
grafana  
influxdb

docs/  
architecture

  
---  
  
# ✨ 최종 목표  

LoL Inhouse Matchmaking Platform

  
기능  
  
- Riot 로그인  
- 자동 팀 밸런스  
- 실시간 내전 대기방  
- Discord 연동  
- 통계 분석

---

💡 추천

옵시디언에서는 이 문서를

LoL-Inhouse-Project.md

로 두고 아래처럼 관리하면 좋습니다.

LoL Project  
 ├ Architecture  
 ├ Backend  
 ├ Frontend  
 ├ Match Algorithm  
 ├ Riot API
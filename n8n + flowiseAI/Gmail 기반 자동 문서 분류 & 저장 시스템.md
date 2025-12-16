# 📁 Gmail 기반 자동 문서 분류 & 저장 시스템

_(n8n + LLM + Google Drive + DB + Teams)_

---

## 1️⃣ 프로젝트 개요 (Overview)

### 📌 목적

Gmail로 수신되는 메일과 첨부파일을 **자동으로 분류**하고  
**중복 없이 Google Drive에 저장**하며  
처리 이력을 **DB에 기록**하고  
결과를 **Teams로 알림**하는 완전 자동화 파이프라인 구축

### 📌 해결하려는 문제

- 메일 수동 확인 및 파일 정리의 비효율
    
- 중복 파일/폴더 생성
    
- 처리 이력 추적 불가
    
- 문서 분류 기준의 일관성 부족
    

---

## 2️⃣ 전체 아키텍처 (Architecture)

`[Gmail]    ↓ (New Mail Trigger) [n8n Workflow]    ├─ DB (처리 이력 조회 / 저장)    ├─ Attachment 여부 판단    ├─ LLM 프롬프트 기반 문서 분류    ├─ Google Drive 폴더/파일 관리    └─ Teams 알림`

### 🔧 사용 기술 스택

- **Automation**: n8n (Self-hosted)
    
- **AI / LLM**: OpenAI (프롬프트 기반 문서 분류)
    
- **Mail**: Gmail Trigger
    
- **Storage**: Google Drive
    
- **Database**: MySQL / PostgreSQL
    
- **Notification**: Microsoft Teams Webhook
    

---

## 3️⃣ 처리 흐름 상세 (Step-by-Step Flow)

---

### ✅ STEP 1. Gmail Trigger – 메일 수신

- Gmail의 새 메일 이벤트를 트리거로 사용
    
- 메일 메타데이터 수신
    

`{   "id": "gmail_message_id",   "threadId": "...",   "internalDate": "timestamp",   "Subject": "...",   "From": "...",   "snippet": "메일 본문 요약" }`

---

### ✅ STEP 2. 처리 이력 기준 필터링

#### 기준

- **DB에 저장된 마지막 internalDate 이후 메일만 처리**
    
- 중복 방지를 위해 `gmail_id`를 기준으로 확인
    

`SELECT MAX(internal_date) FROM mail_process WHERE status = 'DONE';`

---

### ✅ STEP 3. 메일 처리 이력 DB 저장

#### 저장 목적

- 중복 처리 방지
    
- 처리 상태 추적
    
- 장애 발생 시 재처리 가능
    

#### 상태값 정의

- `PROCESSING` : 처리 중
    
- `DONE` : 정상 완료
    
- `ERROR` : 처리 실패
    

`INSERT INTO mail_process (   gmail_id,   subject,   sender,   internal_date,   status ) VALUES (...) ON DUPLICATE KEY UPDATE status = status;`

---

### ✅ STEP 4. 첨부파일 존재 여부 확인

- `payload.mimeType`
    
- `parts[].filename` 여부로 판단
    

`첨부파일 없음 → 상태 DONE 첨부파일 있음 → 분류 단계로 이동`

---

### ✅ STEP 5. LLM 프롬프트 기반 문서 분류

#### 입력값

- 메일 제목 (Subject)
    
- 메일 본문 (snippet)
    
- 첨부파일명
    

#### 프롬프트 역할

- 프로젝트명 추론
    
- 문서 유형 분류
    
- 연/월 추출
    
- 저장 경로 결정
    

#### 출력(JSON)

`{   "projectName": "프로젝트C",   "year": "2025",   "month": "01",   "docType": "보고서",   "finalFolderPath": "/프로젝트C/2025/01/보고서",   "newFileName": "프로젝트C_초안.pdf" }`

---

### ✅ STEP 6. Google Drive 파일 저장

#### 로직

1. 폴더 경로 존재 여부 확인
    
2. 없으면 생성
    
3. 동일 파일명 존재 시 `_2`, `_3` suffix 부여
    
4. 파일 업로드
    

`계약서.pdf 계약서_2.pdf 계약서_3.pdf`

---

### ✅ STEP 7. Teams 알림 발송

#### 알림 내용

- 메일 제목
    
- 프로젝트명
    
- 문서 유형
    
- 저장 경로
    
- 파일명
    

`📄 문서 자동 저장 완료 - 프로젝트: 프로젝트C - 문서유형: 보고서 - 경로: /프로젝트C/2025/01/보고서`

---

### ✅ STEP 8. DB 상태 업데이트

`UPDATE mail_process SET status = 'DONE' WHERE gmail_id = :gmail_id;`

---

## 4️⃣ 핵심 설계 포인트 (Why This Design)

### ✔ 중복 방지

- gmail_id UNIQUE
    
- internalDate 기준 필터링
    

### ✔ 확장성

- LLM 프롬프트만 수정하면 분류 정책 변경 가능
    
- Drive → S3 / SharePoint로 확장 가능
    

### ✔ 안정성

- 상태 기반 처리 (PROCESSING / DONE / ERROR)
    
- 재실행해도 중복 처리 없음
    

---

## 5️⃣ 이력서용 요약 문구 (바로 사용 가능)

> **Gmail 기반 자동 문서 분류 및 저장 시스템 구축**
> 
> n8n을 활용해 Gmail 수신 메일을 자동 트리거로 처리하고,  
> LLM 프롬프트 기반 문서 분류 로직을 적용하여  
> Google Drive에 중복 없는 폴더/파일 구조로 저장하는 자동화 시스템을 설계·구현.
> 
> 처리 이력을 DB로 관리하여 중복 처리 및 재실행 안정성을 확보했으며,  
> 결과를 Teams로 실시간 알림하는 업무 자동화 파이프라인 구축.

---

## 🔜 다음 실제 작업 추천

1️⃣ **첨부파일 파싱 로직 구현**  
2️⃣ **LLM 프롬프트 실제 적용 (OpenAI 노드)**  
3️⃣ **Drive 중복 파일명 처리 Code 노드 작성**

원하면 다음 단계는  
👉 **STEP 4 ~ 6을 n8n 노드 단위로 실제로 같이 만들어줄게.**
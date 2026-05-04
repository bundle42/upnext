# UpNext
- https://42upnext.com/
- 뉴스 기반 주가 예측 플랫폼

- 문제 정의: 뉴스 데이터만으로는 주가 방향성을 정량적으로 반영하기 어려움
- 솔루션: 뉴스 감성 데이터를 날짜별 시계열 feature로 변환 + LSTM 기반 예측
- 성과: Baseline 대비 약 8% 성능 개선 (55% → 63%)
- 기술 스택: React, Spring Boot, FastAPI, LSTM, AWS Infra
- 역할: 전체 시스템 설계, Backend/AI 서버 핵심 기능 개발, Frontend 주요 기능 구현

## 🧠 Summary
뉴스 감성 데이터를 시계열 feature로 재구성하여, 주가 상승/하락 예측 정확도를 개선한 AI 기반 웹 플랫폼

## 🧩 Architecture
1. 프론트엔드에서 뉴스 수집 요청 (관리자 기능)
2. Spring Backend에서 뉴스 데이터 저장 및 전처리
3. FastAPI AI Server로 감성 분석 및 예측 요청
4. LSTM 기반 주가 상승/하락 예측 수행
5. 결과를 Backend를 통해 Frontend에 반환 및 시각화

## 🔗 Repositories
- Frontend: http://github.com/bundle42/upnext-frontend
- Backend: https://github.com/bundle42/upnext-backend
- AI Server: https://github.com/bundle42/upnext-ai

## 🚀 주요 기능
- 뉴스 텍스트 기반 감성 분석
- LSTM 모델을 활용한 주가 상승/하락 예측
- 예측 결과 시각화
- 뉴스 데이터 수집 (Naver API 활용)
- AWS 기반 클라우드 인프라 구성

## ⚙️ 기술 스택
- React (Vite, TS): 사용자 인터페이스 구현
- Spring Boot: REST API 및 서버 로직 처리
- FastAPI: AI 모델 서빙 및 예측 API
- LSTM: 시계열 데이터 기반 주가 방향 예측
- MySQL: 데이터 저장 및 관리
- Infra: AWS EC2, RDS, S3, Nginx
- 기타: Certbot, Cloudflare
- 보안: Spring Security 기반 세션 인증 (JSESSIONID 쿠키 사용)
  - 인증이 필요한 API는 필터에서 접근 제어
  
## 👨‍💻 My Role
- 전체 시스템 설계 주도
- Backend / AI 서버 핵심 기능 개발
- Frontend 주요 기능 구현
- 뉴스 감성 데이터를 시계열로 재구성하여 주가 예측 모델 성능 개선

## 🚀 Deployment

### Infrastructure
- EC2 #1: Frontend + Spring Backend
- EC2 #2: FastAPI AI Server

### CI/CD (GitHub Actions)
- 코드 push 시 자동 실행
  - Frontend: build 후 dist 파일 배포, Nginx 재시작
  - Backend: Spring Boot 빌드 후 app.jar 배포 및 서비스 재시작
  - AI Server: FastAPI 코드 업데이트 및 서버 재시작

### Server Management
- Nginx를 통한 정적 파일 및 프록시 관리
- systemd를 이용한 Spring 서버 실행 관리
- nohup을 이용한 FastAPI 서버 실행

### Logs
- Spring: `journalctl -u app -f`
- FastAPI: `tail -f uvicorn.log`

## 🌐 배포 구성

| 구성 요소                | 서버            |
| ------------------------ | --------------- |
| Frontend (React + Nginx) | EC2 #1          |
| Backend (Spring Boot)    | EC2 #1          |
| AI Server (FastAPI)      | EC2 #2          |
| Database                 | AWS RDS (MySQL) |
| Storage                  | AWS S3          |

EC2 인스턴스를 통해 서비스 운영 및 원격 배포 환경 구성

## ⚙️ 로컬 실행

- run.bat
- 환경변수 설정

- upnext-frontend
- npm run dev

- upnext-backend
- gradlew bootRun

- upnext-ai
- venv\Scripts\activate && uvicorn main:app --reload --port 8000

## 📂 업로드 경로

- 환경 경로
- 로컬 C:/Users/LENOVO/springboot_img
- EC2 /home/ec2-user/uploads

- Nginx 설정
- /etc/nginx/nginx.conf

- Spring 환경변수 설정
- /home/ec2-user/app/app.env

## 🔗 프록시 설정

- Vite
- /api → localhost:8080
- /uploads → localhost:8080
- Spring
- /uploads/\*\* → file:UPLOAD_DIR/
- Nginx (EC2)
- /uploads → 로컬 파일 경로
- /api → Spring 서버

## 📰 News Data Pipeline

### 1. 과거 뉴스 수집 (BigKinds)
- BigKinds에서 뉴스 데이터를 Excel 형태로 수집
- `bigkinds_sentiment.py` 실행하여 감성 분석 CSV 생성
- 종목별 CSV 파일 생성 (삼성전자, SK하이닉스, 현대차)
- CSV 컬럼:
  - `date, title, label, confidence, sentiment_score, contents, newsLink, searchQuery`
- 생성된 CSV는 환경변수 `UPLOAD_DIR` 경로로 이동

👉 이후  
- `/api/admin/news/csvimport` API를 통해 DB 저장

### 2. 실시간 뉴스 수집 (Naver API)
- 프론트에서 `/api/admin/news/import` 호출
- Spring의 `NaverNewsService`에서 뉴스 수집
  - 종목별 최신 뉴스 약 100건

#### 🔹 감성 분석 흐름
1. 뉴스 제목 리스트 생성
2. FastAPI (`/analyze-batch`)로 전송
3. 감성 점수 반환

#### 🔹 저장
- 뉴스 정보 + 감성 점수를 DB (`board_table`)에 저장

## 📈 Stock Prediction Pipeline

### 1. 예측 요청
- 프론트에서 `/api/predict` 호출

### 2. Feature Engineering (Spring)
- 날짜별 뉴스 감성 점수 집계
- LSTM 입력을 위한 feature 생성

### 3. AI 서버 처리 (FastAPI)
- `/predict` API 호출
- 종목별 주가 데이터 로드
- 뉴스 감성 데이터와 merge

### 4. LSTM 예측
- 시계열 데이터 기반 상승/하락 예측 수행
- 결과를 Spring → Frontend로 반환

## 💡 Key Design Point
- 뉴스 감성 데이터를 단순 점수가 아닌 "날짜별 집계된 시계열 feature"로 변환하여 LSTM 입력으로 활용
- 과거 뉴스(BigKinds) + 실시간 뉴스(Naver API)를 결합하여 데이터 부족 문제 해결
- AI 서버를 FastAPI로 분리하여 모델 서빙과 서비스 로직을 독립적으로 운영

## 📊 Model Performance
- Accuracy: 약 60~65% 수준 (상승/하락 예측 기준)
- Baseline: 전일 종가 대비 상승/하락 기준 (약 50~55%)
- 뉴스 감성 feature 포함 시 예측 정확도 상승
- 금융 시계열 데이터의 특성상 완벽한 예측이 어려운 환경에서, 랜덤 대비 유의미한 성능 향상 확인

### 🔍 Ablation Study
- 주가 데이터만 사용: 약 55%
- 뉴스 감성 + 주가 데이터: 약 63%
- Baseline 대비 약 8% 성능 개선
- 문제점(시장은 뉴스 외 변수 많음, 감성 분석 noise 있음, 과적합 가능성)

## ❗ Problem
- 뉴스 데이터만으로는 주가 방향성을 정량적으로 반영하기 어려움
- 감성 분석 결과를 단순 점수로 사용할 경우 시계열 패턴 반영이 어려움
- 데이터 부족으로 인해 모델 학습 성능 한계 존재

## ✅ Solution
- 뉴스 감성 데이터를 날짜별로 집계하여 시계열 feature로 변환
- BigKinds + Naver API를 결합하여 데이터 수 확대
- LSTM을 활용하여 시간 의존성을 반영한 예측 수행

## 🔍 개선/확장 아이디어
- 모델 확장: LSTM에서 Transformer 기반 시계열 모델(TFT, Informer 등)로 확장하면 장기 의존성 반영에 더 유리할 수 있음
- 멀티모달 데이터: 뉴스 텍스트 외에도 트위터, 공시, 경제 지표 같은 다른 신호를 feature로 추가하면 예측력이 강화될 가능성
- MLOps 체계화: 모델 버전 관리, 자동 재학습 파이프라인, 성능 모니터링을 도입하면 운영 안정성이 크게 향상

## 🚀 활용 가능성
- 실제 투자 의사결정보다는 시장 반응을 보조적으로 해석하는 지표로 적합
- 금융 데이터 특성상 완벽한 예측은 어렵지만, baseline 대비 성능 개선이 있다는 점에서 연구/실험적 가치가 큼

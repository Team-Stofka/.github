## 1. 프로젝트 개요 📈🚀
Stofka는 Upbit API를 활용해 📡 실제 거래소의 코인 데이터를 수집하고, 이를 Kafka 기반 데이터 파이프라인으로 실시간 처리하여 📊 시각화하는 프로젝트입니다.

이 프로젝트의 핵심 목적은

✅ 대용량 실시간 데이터 처리 경험을 쌓고, <br>
✅ 안정적인 스트리밍 아키텍처를 직접 구현하는 것입니다.

### 🔍 주요 특징
- 📥 실시간 코인 시세 수집 및 가공
- 🧵 Kafka를 통한 데이터 스트리밍 처리
- 🖥️ 차트 UI를 통한 실시간 시세 시각화
- ⚙️ 고속 데이터 흐름에 적합한 구조 설계 및 구현

<br>

## 2. 프로젝트 설명 ✨

> **실시간 비트코인 시세 시각화 시스템**
> 외부 API에서 수신한 비트코인 시세 데이터를 Kafka로 처리하고,
> 이를 실시간으로 사용자에게 시각화하여 제공하는 **데이터 스트리밍 플랫폼**입니다.

### 🎥 주요 화면

<div align="center"> <img src="https://github.com/user-attachments/assets/4b68c28a-0ac8-4873-b2fe-59223dd29356" alt="실시간 초봉 시세 차트" width="600"/> <br/> <sub>🔼 실시간 그려지는 초봉 차트</sub> </div>

<br>

<div align="center"> <img src="https://github.com/user-attachments/assets/30f94ddd-aabe-4701-87db-6e76d80225ec" alt="초봉 기능 상세 예시" width="600"/> <br/> <sub>🔼 초봉 세부 기능</sub> </div>

<br>

<div align="center"> <img src="https://github.com/user-attachments/assets/9f2e05c3-b5de-4d6b-a430-d5cb0feb573d" alt="Upbit vs Stofka 비교" width="600"/> <br/> <sub>🔼 업비트 실제 시세와 우리 서비스의 초봉 일치 여부 비교</sub> </div>

### 🧩 핵심 구성

* 📡 **실시간 데이터 수집**
  외부 WebSocket API로부터 비트코인 시세 데이터를 수신

* 🔁 **Kafka 기반 스트리밍 처리**
  수신한 데이터를 Kafka로 전송하여 안정적인 비동기 처리

* 📤 **SSE(Server-Sent Events) 전송**
  Kafka Consumer가 데이터를 처리하여 클라이언트에 실시간 푸시

* 📊 **프론트엔드 실시간 시각화**
  React + Chart.js 기반의 실시간 비트코인 시세 차트 구현

* 🛠️ **모니터링 시스템 구축**
  Prometheus와 Grafana를 활용해 **Kafka, PostgreSQL, Redis, WAS Pod**의 성능과 상태를 시각화

<br>

## 3. 팀원 소개 🎈

|<img src="https://github.com/EOTAEGYU.png" width="220" />|<img src="https://github.com/wns5120.png" width="220" />|<img src="https://avatars.githubusercontent.com/u/73926352?v=4" width="220" />|<img src="https://avatars.githubusercontent.com/u/114637614?v=4" width="220" />|
|:-:|:-:|:-:|:-:|
|어태규<br/>[@EOTAEGYU](https://github.com/EOTAEGYU)|유호준<br/>[@wns5120](https://github.com/wns5120)|임하은<br/>[@imhaeunim](https://github.com/imhaeunim)|오현두<br/>[@HyunDooBoo](https://github.com/HyunDooBoo)|

<br>

## 4. 기술 스택 🛠️

### 📌 백엔드 & 데이터 처리
- **Spring Boot**: 캔들 생성 로직 및 Kafka Consumer 구성
- **Kafka / Kafka Connect**: 실시간 시세 데이터 스트리밍 및 PostgreSQL 연동
- **PostgreSQL**: 분/시/일봉 데이터 저장
- **Redis**: 실시간 데이터 캐싱 및 중간 상태 저장

### 📊 모니터링 & 시각화
- **Grafana**: 시스템 지표 시각화
- **Prometheus**: 메트릭 수집 및 Grafana 연동
- **Alertmanager**: 이상 상황 알림 설정

### ⚙️ 인프라
- **Kubernetes (K8s)**: 서비스 컨테이너 오케스트레이션
- **ArgoCD**: GitOps 방식의 자동 배포
- **NGINX**: 리버스 프록시 및 트래픽 라우팅
- **Git**: 버전 관리

### 🌐 프론트엔드
- **사용스택**: 사용자용 시각화 페이지 구성

<br>

## 5. 아키텍처 📊
![image](https://github.com/user-attachments/assets/6068a08f-e628-4fba-b874-e0a48f418df3)

<br>

## 6. 데이터 흐름 및 구성 🧩

### 1️⃣ 실시간 데이터 수집
- **업비트 WebSocket API**를 통해 초단위 `candle` 데이터를 수신
- 수신된 데이터는 **Kafka Producer**가 `market_ticker` 토픽에 전송

### 2️⃣ Kafka 메시지 처리
- Kafka Consumer 그룹이 데이터를 구독
- **Redis**에 최신 시세 상태 저장 (현재가, 전일가, 초캔들 데이터)
- 동시에 PostgreSQL에 초봉 데이터 저장 (`market_candle_1s`)

### 3️⃣ 캔들 데이터 생성
- **pg_cron**을 사용해 일정 주기마다 분/시/일봉 데이터를 생성
  - `1분 → 3분 → 5분 → ... → 일봉` 순으로 계층적 집계
- 생성된 캔들은 PostgreSQL의 각각의 테이블에 저장

### 4️⃣ 캔들 데이터 요청 처리 
- 프론트엔드에서 특정 종목과 캔들 간격(예: 1분, 3분 등)을 요청하면,
Redis에서 우선적으로 데이터를 조회
- Redis에 존재하지 않으면, PostgreSQL에서 가장 최신의 N개 데이터를 조회
- 조회된 데이터는 Redis에 다시 캐싱하여 다음 요청 시 빠른 응답 가능
- 이 구조를 통해 성능 최적화와 데이터 일관성을 동시에 달성

### 5️⃣ 데이터 시각화 
- SSE(Server-Sent Events)를 통해 클라이언트에 실시간 시세 및 캔들 데이터 전송
- Kafka 메시지를 종목별로 분기해 ticker, candle, postgres-candle 이벤트로 전달
- 클라이언트는 수신 데이터를 차트에 실시간 반영하여 지연 없이 시각화

### 6️⃣ 경고 및 자동화
- **Alertmanager**가 이상 징후 감지 시 알림 전송
- **ArgoCD**를 통해 GitOps 기반 CI/CD 구성 

<br>

## 7. 트러블 슈팅 및 기술적 고민

### **8-1. Kafka 브로커 운영 전략**

- 3개의 브로커를 사용하여 Kafka 클러스터를 구성했으며, 브로커 장애가 일어나도 정상적으로 운영할 수 있는 고가용성 환경을 구성했습니다.

<br>

📜 [고가용성 Kafka 클러스터 구성](https://github.com/Team-Stofka/stofka-docs/blob/main/kafka/Kafka%20%EB%B8%8C%EB%A1%9C%EC%BB%A4%203%EA%B0%9C%2C%20Kafdrop%20%EC%84%A4%EC%B9%98.md)

- 카프카의 안정적인 운영을 위해 prometheus에서 metric을 수집하고 grafana에 저장소로 등록하여 대시보드를 생성했습니다.

<br>

📜 [카프카 데이터 수집 및 모니터링 설정](https://github.com/Team-Stofka/stofka-docs/blob/main/kafka/Kafka%20Metric%20%20%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81%20%EC%84%A4%EC%A0%95.md)

### **8-2. PostgreSQL & Redis 역할 분리**

영속 데이터는 PostgreSQL에, 휘발성 실시간 데이터는 Redis에 저장하여 부하를 분산했습니다. Redis에는 TTL과 메모리 관리 정책을 도입했습니다.

[문서.md](http://문서.md)

### **8-3. 캔들 집계 처리 방식**

#### 🚨문제 상황
- 업비트 API에서 1/3/5분 등 캔들 데이터를 제공하지만 API 호출로 매번 데이터를 받아와야 합니다. 또한 호출 제한 횟수가 있어 실직적으로 사용하기 어렵습니다.

#### ✅해결 방법
- 실시간으로 들어오는 1초 단위 캔들 데이터를 PostgreSQL에 저장 후, 1초 데이터 60개로 1분 캔드 생성, 1분 봉으로 3/5/15분 캔들을 생성하며 1일 캔들까지 생성합니다.
- PostgreSQL에서 pg_cron을 사용하여 자동으로 캔들을 생성하였습니다.

아래 문서를 통해 캔들 생성 과정을 자세히 확인할 수 있습니다.

📜 [캔들 생성 자동화 문서](https://github.com/Team-Stofka/stofka-docs/blob/main/db/candle-aggregation.md)

<br>

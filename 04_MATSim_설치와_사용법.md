# 04. MATSim 설치와 사용법

> **MATSim (Multi-Agent Transport Simulation)** — 대규모 행위자 기반 교통 시뮬레이션
> 개발: TU Berlin / ETH Zurich 주도 커뮤니티
> 라이선스: **GPL v2** (완전 오픈소스, 무료)
> 공식 사이트: https://www.matsim.org

---

## 1. MATSim이란?

- 수백만 명의 개별 **"행위자(agent)"** 각각이 하루 일과 계획(집→직장→상점→집)을 갖고,
  **반복 시뮬레이션(iteration)** 을 통해 교통 흐름·수요가 균형(equilibrium)에
  도달하도록 하는 **행위자 기반(agent-based) 시뮬레이터**입니다.
- 신호·교차로 단위의 미시 분석보다 **도시·광역권 단위의 정책 평가**에 강점입니다.
  - 예: 혼잡통행료, BRT(간선급행버스) 도입, 자율주행 택시(MaaS) 배치, 전기차 충전 인프라,
    배출가스·소음 영향 평가
- Java 기반 → Windows/Linux/macOS 어디서든 실행 가능.
- **MATSim + SUMO 연동**: 도시 전체는 MATSim(빠름), 핵심 지역 교차로는 SUMO(정밀)
  하이브리드 구성도 가능.

### 매 반복(iteration)에서 일어나는 일
1. **Mobility Simulation (Mobsim)**: 모든 행위자가 자신의 계획을 네트워크에서 실행
   (기본 모델 QSim: 큐 기반 차량흐름)
2. **Scoring**: 각 계획을 효용(utility) 함수로 채점 (통행시간·지각·비용 등을 벌점)
3. **Replanning**: 일부 행위자(보통 10~20%)가 출발시각·경로·수단을 바꿔 시도
4. 균형 도달까지 반복 (보통 100~1000회)

---

## 2. 설치

### 2-1. 사전 준비
- **Java 17 이상** (JDK) 설치 필요 → https://adoptium.net/ (Temurin) 권장
- (선택) Maven — 예제를 직접 빌드/확장할 때
- (선택) Python — 데이터 전처리·시각화용

확인:
```
java -version
mvn -version   # (선택)
```

### 2-2. 설치 방법 (두 가지)

**방법 A — 예제 패키지 다운로드 (가장 간단)**
1. https://www.matsim.org/docs/tutorials/ 에서
   [Quick Start / Get Started] 페이지의 다운로드 링크 확인
2. 예제가 포함된 Maven 프로젝트 zip 내려받기 (일반적으로 GitHub의
   `matsim-org/matsim-example-project`)
   ```
   git clone https://github.com/matsim-org/matsim-example-project.git
   ```
3. 프로젝트 폴더에서 빌드:
   ```
   mvn package
   ```
4. 실행:
   ```
   mvn exec:java -Dexec.mainClass="org.matsim.run.RunMatsim" \
     -Dexec.args="scenarios/equil/config.xml"
   ```

**방법 B — Maven 의존성으로 직접 사용 (개발자용)**
`pom.xml`에 추가:
```xml
<dependency>
    <groupId>org.matsim</groupId>
    <artifactId>matsim</artifactId>
    <version>15.0</version>
</dependency>
```

---

## 3. 첫 시뮬레이션 실행 (equil 예제)

`equil`은 MATSim에 기본 포함된 가장 단순한 시나리오(직선 도로 1개)입니다.

### 3-1. 폴더 구조 (matsim-example-project/scenarios/equil)
```
config.xml          → 전체 설정 (네트워크·수요·반복 횟수·출력 등)
network.xml         → 도로망
plans.xml           → 행위자의 일과 계획(수요)
```

### 3-2. 실행
```
mvn exec:java -Dexec.mainClass="org.matsim.run.RunMatsim" -Dexec.args="scenarios/equil/config.xml"
```

### 3-3. 출력
- `output/` 폴더가 생성되고 다음을 산출:
  - `output_plans.xml` — 반복 후 최종 계획(경로·수단·출발시각)
  - `output_events.xml.gz` — 모든 사건(통행 시작/종료, 링크 진입/이탈) 기록 → **핵심 결과 데이터**
  - `output_network.xml` — 최종 네트워크
  - `scorestats.txt` — 반복별 평균 점수 (수렴 확인)
  - `traveldistancestats.txt`, `traveltimestats.txt`, `modestats.txt` 등

### 3-4. 반복 성능 확인
`scorestats.txt`에서 평균 점수가 반복이 진행될수록 증가 후 안정화되는지 확인합니다
(균형 수렴). 평균 점수가 오르지 않으면 설정을 조정합니다.

---

## 4. 핵심 입력 파일 이해

### 4-1. config.xml (설정)
```xml
<config>
  <module name="controler">
    <param name="outputDirectory" value="output/equil" />
    <param name="lastIteration" value="150" />
    <param name="runId" value="equil" />
  </module>

  <module name="network">
    <param name="inputNetworkFile" value="network.xml" />
  </module>

  <module name="plans">
    <param name="inputPlansFile" value="plans.xml" />
  </module>

  <module name="qsim">
    <param name="flowCapacityFactor" value="0.05" />
    <param name="storageCapacityFactor" value="0.05" />
    <param name="startTime" value="00:00:00" />
    <param name="endTime" value="30:00:00" />
  </module>
</config>
```

### 4-2. plans.xml (행위자 수요)
```xml
<population>
  <person id="1">
    <plan selected="yes">
      <act type="home" x="0" y="0" end_time="08:00:00" />
      <leg mode="car" />
      <act type="work" x="1000" y="0" dur="08:00:00" />
      <leg mode="car" />
      <act type="home" x="0" y="0" />
    </plan>
  </person>
</population>
```
- `act`: 활동 (type, 좌표, 시간)
- `leg`: 활동 사이 통행 (mode: car / pt / walk / bike / drt)

### 4-3. network.xml (도로망)
```xml
<network>
  <node id="1" x="0" y="0" />
  <node id="2" x="1000" y="0" />
  <link id="1_2" from="1" to="2" length="1000.0" capacity="1800.0"
        freespeed="13.9" permlanes="1" modes="car,bike" />
</network>
```
- `capacity`: 시간당 차량 수 (대략 도로 용량)
- `freespeed`: 자유속도(m/s), `permlanes`: 차로 수, `modes`: 허용 수단

---

## 5. 대중교통(GTFS) 및 고급 시나리오

### 5-1. GTFS → MATSim
`pt2matsim` 툴로 GTFS 대중교통 스케줄을 MATSim transit 스케줄로 변환:
```
java -jar pt2matsim-...jar path/to/gtfs/ transitSchedule.xml transitVehicles.xml
```

### 5-2. 공개 시나리오 활용
- **MATSim Open Berlin Scenario**: 500만 행위자, 캘리브레이션 완료
  → https://github.com/matsim-scenarios
- Swiss National Model (SBB 운영), ARUP의 뉴질랜드 모델 등

### 5-3. 주요 확장(contribs)
| 확장 | 용도 |
|---|---|
| DVRP / AMoDeus | 자율 모빌리티 온디맨드(MoD) 택시/쉐어링 |
| Emissions | 링크 단위 배출가스(HBEFA) |
| Noise | 소음 노출 평가 |
| Freight | 화물·배송 계획 |
| Bicycle | 자전거 인프라·경사 효과 모델 |
| Signals | 신호등 시뮬레이션 |

---

## 6. 시각화

- **OTFVis (오픈소스)**: 실행 중·사후 애니메이션 (이벤트 파일 로드)
  - 실행: `mvn exec:java -Dexec.mainClass="org.matsim.vis.otfvis.OTFVisFileWriter" -Dexec.args="output/output_events.xml.gz"`
- **Senozon Via (상용)**: MATSim 개발자들이 만든 강력한 사후 분석·시각화 도구
- Python(matplotlib/pandas) + events 파일 직접 분석으로 커스텀 차트 생성

---

## 7. SUMO와의 비교 요약 (어떤 걸 쓸까)

| 항목 | MATSim | SUMO |
|---|---|---|
| 시뮬레이션 단위 | 행위자(사람)의 하루 일과 | 차량 1대의 주행 |
| 교차로/신호 미시 분석 | 약함 (신호 모듈은 제한적) | **매우 강함** |
| 도시·정책 수요 시나리오 | **매우 강함** | 중간 |
| 실행 속도 (대규모) | 빠름 (수백만 명 가능) | 중간 |
| 사용 난이도 | 높음 (Java·XML 설정) | 중간 (GUI 존재) |
| 시각 편집기 | 없음 (파일 기반) | **netedit 존재** |

> **결론**: 도로·교차로 단위의 Vissim 대체가 목적이면 **SUMO**,
> 도시 전체 정책/수요 평가가 목적이면 **MATSim**을 선택하세요.

---

## 8. 학습 리소스
- 설치/튜토리얼: https://www.matsim.org/docs/tutorials/
- 공식 문서: https://www.matsim.org/docs/
- 책 (무료 PDF): The Multi-Agent Transport Simulation MATSim — https://www.matsim.org/the-book
- 포럼/메일링 리스트: https://www.matsim.org/mailinglist
- GitHub: https://github.com/matsim-org/matsim-libs
- 예제 프로젝트: https://github.com/matsim-org/matsim-example-project

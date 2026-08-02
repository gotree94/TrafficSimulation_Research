# 03. Eclipse SUMO 설치와 사용법

> **SUMO (Simulation of Urban MObility)** — PTV Vissim의 가장 대표적인 오픈소스 대안
> 개발: 독일항공우주센터(DLR) / 라이선스: EPL 2.0 (GPL 2+ 이차 라이선스)
> 공식 문서: https://sumo.dlr.de/docs/index.html

---

## 1. 설치 (Windows 기준)

### 1-1. 사전 준비
- Windows 10/11 64bit
- (권장) Python 3.x — SUMO Python 툴(osmWebWizard, TraCI 등) 사용에 필요
- 인터넷 연결

### 1-2. 설치 방법 (가장 간단)

가장 쉬운 방법은 **"all-inclusive" 패키지**를 내려받는 것입니다.
(이 패키지에는 bin, tools, docs, 예제가 모두 포함되어 있으며 컴파일할 필요가 없습니다.)

1. SUMO 공식 다운로드 페이지 접속
   - https://sumo.dlr.de/docs/Downloads.html
   - 또는 https://eclipse.dev/sumo/ 의 Downloads 항목
2. 최신 **Windows x64 설치 프로그램** 다운로드 (예: `sumo-win64-...zip` 또는 `.exe`)
3. 압축 풀기 (예: `C:\sumo`) 또는 설치 마법사 진행
4. **환경변수 `SUMO_HOME`** 설정:
   - `C:\sumo` 를 가리키도록 설정 (또는 명령줄에서 `set SUMO_HOME=C:\sumo`)
   - `C:\sumo\bin` 을 `PATH`에 추가 → 터미널에서 `sumo`, `sumo-gui`, `netedit` 실행 가능
5. 검증:
   ```
   sumo --version
   ```
   버전이 출력되면 성공.

> 소스에서 직접 빌드하려면 Visual Studio Community + CMake + SUMOLibraries 필요
> (https://sumo.dlr.de/docs/Installing/Windows_Build.html). 일반 사용자는 불필요.

### 1-3. Python 툴 설치 (권장)
```
pip install -r C:\sumo\tools\requirements.txt
```
- osmWebWizard(OSM 시나리오 자동 생성), TraCI(자동화), 분석 툴 등이 동작합니다.

### 1-4. 핵심 실행 파일 요약

| 파일 | 역할 |
|---|---|
| `sumo.exe` | 명령줄(비GUI) 시뮬레이션 엔진 |
| `sumo-gui.exe` | 그래픽 시뮬레이션 뷰어 (차량 애니메이션) |
| `netedit.exe` | **그래픽 네트워크/수요 편집기** (Vissim 네트워크 편집에 대응) |
| `netconvert.exe` | 도로망 변환·가져오기 (OSM, Vissim, shapefile 등) |
| `netgenerate.exe` | 추상 네트워크(그리드, 스파이더) 자동 생성 |
| `duarouter.exe` | 최단경로 경로배정 |
| `jtrrouter.exe` | 회전비율 기반 경로 생성 |
| `od2trips.exe` | O/D 행렬 → 통행(trip) 변환 |
| `polyconvert.exe` | 배경 지도(polygon) 변환 |

---

## 2. 첫 시뮬레이션 만들기 (Hello World)

SUMO 시뮬레이션에 필요한 최소 파일 3종:
1. **네트워크**: `*.net.xml` — 노드(junction) + 엣지(street)
2. **수요(차량/경로)**: `*.rou.xml`
3. **시뮬레이션 설정**: `*.sumocfg`

### 2-1. netedit로 네트워크 생성
1. `netedit.exe` 실행 → `File → New Network` (`Ctrl+N`)
2. **Network** 모드에서 노드 3개 생성 (도구: `N` 또는 "Create Edges")
   - `Shift+클릭` 으로 여러 노드를 찍고, 좌클릭으로 노드 사이에 엣지 생성
   - 엣지(도로) 2개: 노드1-2, 노드2-3
3. 왼쪽 속성 패널에서 노드/엣지 ID·좌표 수정
   - 노드 좌표 예: 1=`(-100,50)`, 2=`(0,50)`, 3=`(100,50)`
   - 엣지 ID 예: `1to2`, `out`
4. `File → Save Network` (`Ctrl+S`) → `helloWorld.net.xml` 저장

### 2-2. 수요(차량/경로) 생성
1. **Demand** 모드 전환 (`Ctrl+W` 또는 화면 상단 모드 선택)
2. **Route 모드** (`R`): 엣지 `1to2` → `out` 순서로 경로 정의 → 경로 ID 부여
3. **Vehicle 모드** (`V`): 경로에 차량 1대 등록
4. `File → Save Demand` → `helloWorld.rou.xml` 저장

> 모든 파일은 **같은 폴더**에 두어야 합니다.

### 2-3. 시뮬레이션 설정(.sumocfg) 및 실행
1. netedit에서 `Edit → Open in sumo-gui` (`Ctrl+T`)
2. sumo-gui가 열리면 `File → Save Configuration` (`Ctrl+Shift+S`) → `helloWorld.sumocfg`
3. Delay를 **80ms 이상**으로 설정 (차량이 보이도록)
4. `Ctrl+A` 또는 ▶ 버튼으로 시뮬레이션 실행

`.sumocfg` 내용 예시 (직접 작성 가능):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <input>
        <net-file value="helloWorld.net.xml"/>
        <route-files value="helloWorld.rou.xml"/>
    </input>
    <time>
        <begin value="0"/>
        <end value="3600"/>
        <step-length value="1.0"/>
    </time>
</configuration>
```
실행: `sumo-gui helloWorld.sumocfg` 또는 `sumo -c helloWorld.sumocfg`

---

## 3. 실무용 시나리오 구축 워크플로

### 3-1. OpenStreetMap에서 바로 시나리오 만들기 (가장 쉬움)
1. `python C:\sumo\tools\osmWebWizard.py`
2. 브라우저가 열리면 **지역 선택** → 원하는 영역 지정
3. **교통 유형 선택**: 차량, 보행자, 대중교통(PT), 자전거 등 체크
4. **도로 유형 선택**: 고속도로, 간선, 보조도로 등 (대규모 시나리오는 필요한 도로만 선택)
5. "Generate Scenario" 클릭 → 수 분 내에 네트워크+수요+배경지도 생성
6. 생성 폴더의 `*.sumocfg`를 sumo-gui로 열어 실행

### 3-2. 기존 Vissim 모델을 SUMO로 가져오기
`netconvert`가 Vissim 네트워크를 변환합니다.
```
netconvert --vissim-files=mynetwork.inpx --output-file=converted.net.xml
```
- 그 외: `--visum-files`, `--osm-files`, `--shapefile-prefix`, `--opendrive-files` 지원
- 대중교통 스케줄은 GTFS로 가져오기 가능

### 3-3. 텍스트(XML)로 네트워크 직접 작성 (고급)
핵심 파일:
- `*.nod.xml` (노드)
  ```xml
  <nodes>
      <node id="1" x="-100" y="50"/>
      <node id="2" x="0" y="50"/>
      <node id="3" x="100" y="50"/>
  </nodes>
  ```
- `*.edg.xml` (엣지: 도로·차로수·속도)
  ```xml
  <edges>
      <edge id="1to2" from="1" to="2" numLanes="2" speed="13.9"/>
      <edge id="out"   from="2" to="3" numLanes="2" speed="13.9"/>
  </edges>
  ```
- 변환: `netconvert --node-files=my.nod.xml --edge-files=my.edg.xml --output-file=my.net.xml`

### 3-4. 수요(차량·흐름) 생성
- 개별 차량: `*.rou.xml`
  ```xml
  <routes>
      <vType id="car" accel="2.6" decel="4.5" maxSpeed="50"/>
      <route id="r1" edges="1to2 out"/>
      <vehicle id="v1" type="car" route="r1" depart="0"/>
  </routes>
  ```
- 차량 흐름(연속 생성): `<flow id="f1" type="car" route="r1" begin="0" end="3600" period="2"/>`
- O/D 행렬 → `od2trips`
- 회전비율 → `jtrrouter`
- 관측 통행량(교통량 계수) → `dfrouter` / `routeSampler.py`
- 대중교통: `<busStop>` + `<ptLine>` 또는 GTFS import

### 3-5. 신호등(TLS) 만들기
- netedit에서 교차로(노드) 클릭 → `Edit → Traffic Lights → Create TLS`
- 기본 신호 프로그램 자동 생성 (예: `rrrGGgrrrGGg` 문자열 = 빨강/녹색 단계)
- 신호 프로그램 XML(`tls.xml`)을 `<additional-files>`로 연결해 상세 제어 가능

---

## 4. TraCI로 Python 자동화하기 (Vissim COM 대응)

TraCI(Traffic Control Interface)는 실행 중인 SUMO를 외부 프로그램(Python)이
제어·계측하도록 하는 TCP 기반 클라이언트/서버 인터페이스입니다.
- **서버**: sumo / sumo-gui (config에 `--remote-port`)
- **클라이언트**: Python 스크립트 (`import traci`)

### 4-1. 기본 연결 코드
```python
import traci

SUMO_BINARY = r"C:\sumo\bin\sumo-gui"
config = "helloWorld.sumocfg"

traci.start([SUMO_BINARY, "-c", config])
step = 0
while step < 3600:
    traci.simulationStep()                # 1스텝 진행
    cars = traci.vehicle.getIDList()      # 현재 차량 목록
    for veh in cars:
        speed = traci.vehicle.getSpeed(veh)
        print(f"t={traci.simulation.getTime()}s veh={veh} speed={speed:.2f}m/s")
    step += 1
traci.close()
```

### 4-2. 신호 제어 예시
```python
import traci
traci.start([SUMO_BINARY, "-c", config])
while True:
    traci.simulationStep()
    tls = traci.trafficlight.getIDList()
    for t in tls:
        # 현재 단계 가져오기/설정
        state = traci.trafficlight.getRedYellowGreenState(t)
        print(t, state)
        # traci.trafficlight.setPhase(t, 2)
    if traci.simulation.getTime() > 120:
        break
traci.close()
```
- 주요 API: `traci.vehicle.getSpeed`, `traci.inductionloop.getLastStepVehicleNumber`,
  `traci.lane.getLastStepVehicleNumber`, `traci.trafficlight.setPhase`,
  `traci.simulationStep`, `traci.close`
- 전체 API 문서: https://sumo.dlr.de/pydoc/traci.html

### 4-3. 실행 예시 (연결 확인)
```python
import traci, sys
import os
os.environ["SUMO_HOME"] = r"C:\sumo"
sys.path.append(os.path.join(os.environ["SUMO_HOME"], "tools"))

traci.start([r"C:\sumo\bin\sumo", "-c", "helloWorld.sumocfg", "--no-step-log"])
traci.simulationStep()
print("version:", traci.getVersion())
traci.close()
```

---

## 5. 출력(결과) 수집

| 출력 파일 | 설명 | 생성 방법 |
|---|---|---|
| `*.fcd.xml` (FCD) | 차량 위치·속도 전체 기록 | `--fcd-output` |
| `tripinfo.xml` | 차량별 출발/도착 시각·통행시간·지체 | `--tripinfo-output` |
| `summary.xml` | 전 차량 집계(속도·점유·혼잡) | `--summary` |
| `*.edg.xml` | 링크별 교통량·통행시간 | `--edgedata-files` |
| `emissions.xml` | 배출가스(CO2, NOx 등) | `--emissions-output` |
| `*.netstate.xml` | 전 객체 상태 스냅샷 | `--netstate-dump` |

명령 예:
```
sumo -c my.sumocfg --tripinfo-output tripinfo.xml --summary summary.xml
```

---

## 6. 보행자·자전거·대중교통·철도 모델

- **보행자**: `person`/`personFlow` 요소, `walk`/`ride` 다리(leg), 횡단보도(`crossing`)
- **자전거**: `vType`에서 `vClass="bicycle"`, 자전거 전용 도로/신호 지원
- **대중교통**: `busStop`·`trainStop`, 노선(`ptLine`), GTFS 스케줄 import
- **철도**: 신호·전차선·역 포함 철도 네트워크 시뮬레이션 (SUMO 2025+)
- **전기차**: 배터리·충전소 모델
- **수로(Waterway)**: 선박 시뮬레이션

---

## 7. 자주 쓰는 명령 요약

```bash
# 기본 실행
sumo -c scenario.sumocfg
sumo-gui -c scenario.sumocfg

# GUI에서 바로 실행 (바탕화면 연결)
sumo-gui scenario.sumocfg

# 출력 수집
sumo -c s.sumocfg --tripinfo-output t.xml --summary sm.xml

# Vissim 모델 변환
netconvert --vissim-files=model.inpx --output-file=model.net.xml

# OSM 시나리오 생성
python C:\sumo\tools\osmWebWizard.py

# 네트워크 검증/도로 통계
netconvert -s my.net.xml --output-file=check.net.xml --verbose
```

---

## 8. 학습 리소스
- 튜토리얼 목록: https://sumo.dlr.de/docs/Tutorials/index.html
  - Hello World, Quick Start, OSMWebWizard, Driving in Circles, Public Transport, TaxiService
- 공식 문서: https://sumo.dlr.de/docs/index.html
- FAQ: https://sumo.dlr.de/docs/FAQ.html
- 사용자 메일링 리스트: https://www.eclipse.org/lists/sumo-user/
- 유튜브: "SUMO Traffic Simulator" (설치·첫 시나리오·TraCI 예제)

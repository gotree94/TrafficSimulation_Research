# 01. PTV Vissim 개요와 라이선스

## 1. PTV Vissim이란?

PTV Vissim은 독일 **PTV Group(PTV Planung Transport Verkehr GmbH)** 이 개발한
**미시적(microscopic) 다중 모드 교통 시뮬레이션 소프트웨어**입니다.

- 전 세계 2,500개 이상 도시, 16,500명 이상의 사용자를 보유한 업계 표준 도구입니다.
- 차량 한 대 한 대를 개별 객체로 모델링하여 운전자 행동, 차로 변경, 신호 제어,
  보행자·자전거·대중교통(버스/트램/철도)·자율주행차(CAV)까지 상호작용을 정밀하게 재현합니다.
- 교통 혼잡, 배출가스, 도로 공간 분배, 신호 최적화, 시나리오(what-if) 평가,
  용량 분석 등에 사용됩니다.
- Vissim은 보행자 시뮬레이션 **PTV Viswalk**, 네트워크 규모 대중교통 계획 **PTV Visum**,
  신호 분석 **PTV Vistro** 등 PTV Traffic Suite 제품군과 연동됩니다.

### 주요 기능
- **미시적 시뮬레이션**: 링크(Link)·커넥터(Connector) 개념으로 도로망을 정밀 구축
- **다중 모드**: 승용차·트럭·버스·트램·철도·보행자·자전거·CAV(MaaS 포함)
- **신호 제어**: 신호등 논리 편집, 외부 신호제어기 연동, V2I 통신 기반 우선 신호
- **동적 경로배정(Dynamic Assignment)** 및 메소스코픽·하이브리드 시뮬레이션
- **배출가스 모델**, 주차 분석, 공항·물류·철도역 특수 시나리오
- **COM API**: Python/VBA 등 외부 프로그램에서 제어·자동화 (Vissim COM)
- **3D 시각화** 및 항공사진 배경 지도 사용

## 2. 무료로 사용할 수 있나요? → 제한적

Vissim은 **상용(유료) 소프트웨어**입니다. 무료로 사용할 수 있는 경로는 다음 3가지뿐이며,
모두 제약이 있습니다.

| 구분 | 대상 | 기간 | 제약 |
|---|---|---|---|
| **Student Version (학생용)** | 대학생·대학원생 (연구 목적) | **1년 무료**, 갱신 가능 | 네트워크 최대 1km×1km / 30 zones, 시뮬레이션 600초(45분 세션), 신호제어기 10개 제한, 보행자·외부 모델·일부 인터페이스 불가, 워터마크 표시 |
| **Thesis License (학위논문용)** | 논문·박사 연구자 | 신청제 | 확장된 접근 제공 (대학 계정으로 신청) |
| **Free Trial / Demo (체험판)** | 누구나 | 약 30일 | 기능 제한형 데모 |
| **Vissim Viewer (데모 라이선스)** | 누구나 | 무기한 | 모델 **열람만** 가능 (편집·시뮬레이션 불가) |

### 학생용 신청 방법
1. PTV 공식 페이지(https://www.ptvgroup.com/en/products/ptv-vissim/student-version) 접속
2. **대학 이메일** 주소, 성명, 대학명, 도시, 국가를 입력해 신청
3. 승인 후 1년간 Vissim + Viswalk 학생 버전 사용 가능
4. 대학에 PTV 학술 라이선스가 있으면 대학 라이선스 서버에서 서브라이선스 대여(Borrow) 가능

### 체험판 신청
- https://www.ptvgroup.com/en/solutions/products/ptv-vissim/demo-version/ 에서 신청
- https://cgi.ptvgroup.com/visionSetups/en/ 에서 설치 파일 다운로드

## 3. 상용 라이선스 가격 (2026년 기준)

**공개 가격표가 없고, 모두 견적(Quote) 방식**입니다. G2/Capterra 등에 따르면 가격은
다음 요소에 따라 결정됩니다.

- 패키지 규모 (아래 3가지 패키지 중 선택)
- 선택 모듈·애드온 (신호 제어, 보행자 Viswalk, BIM import 등)
- 사용자 수, 라이선스 방식

### 패키지 구성 (공식 안내 기준)

| 기능 | 기본 패키지 | 중간 패키지 | 상위 패키지 |
|---|---|---|---|
| 미시적 시뮬레이션 | ✓ | ✓ | ✓ |
| 대중교통(Public transport) | 애드온 | ✓ | ✓ |
| 프로그래밍 인터페이스(COM API) | – | ✓ | ✓ |
| 신호 제어(고급) | – | ✓ | ✓ |
| 외부 신호 인터페이스 | 애드온 | ✓ | ✓ |
| 메소스코픽(네트워크) 시뮬레이션 | – | – | ✓ |
| PTV Viswalk (보행자) | 애드온 | 애드온 | 애드온 |

### 라이선스 유형
1. **Perpetual License (영구)**: 구매 후 계속 사용 (장기 사용에 적합)
2. **Subscription License (구독)**: 기간제, 단기·프로젝트용
3. **Customized Package**: 다중 사용자 팀·연구기관·대규모 프로젝트용

> 대략적인 시장 기준가는 네트워크 규모·모듈에 따라 수천만 원에서 수억 원대까지
> 차이가 있으며, **공식 가격은 PTV 영업팀에 문의해야 확정**됩니다.
> 실제 구매가 필요하면 https://www.ptvgroup.com/en/products/ptv-vissim/pricing 에서
> 견적 요청(Request a quote)을 하면 됩니다.

## 4. 결론

- **비상업적 학습·연구**: 학생용 1년 무료 라이선스로 충분히 가능 (단, 1km²·600초 제한)
- **일반 사용자/업무용**: 무료 경로가 사실상 없음 → **오픈소스 대안(SUMO 등) 검토 권장**
- **상세한 기능을 무료로**: 다음 문서 [02_오픈소스 대안 비교](./02_오픈소스_교통시뮬레이션_대안_비교.md) 참고

## 참고 링크
- 제품 페이지: https://www.ptvgroup.com/en/products/ptv-vissim
- 학생용: https://www.ptvgroup.com/en/products/ptv-vissim/student-version
- 견적/가격: https://www.ptvgroup.com/en/products/ptv-vissim/pricing
- 체험판: https://www.ptvgroup.com/en/solutions/products/ptv-vissim/demo-version
- 설치 파일: https://cgi.ptvgroup.com/visionSetups/en/

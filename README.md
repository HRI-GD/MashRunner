# MashRunner

**A와 D를 번갈아 눌러 속도를 높이고 결승선을 향해 달리는 2D 경주 게임**입니다.

Unreal Engine 5.6과 Blueprint를 사용해 교대 입력, 가속·감속 곡선, 달리기 애니메이션, 결승선 판정을 구성했습니다. 입력 빈도와 순서가 캐릭터의 움직임으로 이어지는 구조를 중심으로 구현했습니다.

## 스크린샷

추가예정

*로컬 프로젝트 실행 화면*

## 프로젝트 개요

- **장르:** 2D 연타 / 달리기 경주
- **엔진:** Unreal Engine 5.6
- **게임플레이 구현:** Blueprint
- **2D 표현:** Paper2D 스프라이트와 플립북
- **입력 처리:** Enhanced Input
- **기본 맵:** `MAP_Field`

## 플레이 방법

1. 게임 화면을 클릭해 입력 포커스를 줍니다.
2. **A → D → A → D** 순서로 두 키를 번갈아 누릅니다.
3. 입력을 이어가며 속도를 유지하고 결승선을 향해 달립니다.

`IA_PowerLeft`와 `IA_PowerRight`를 `IMC_Runner`에 연결해 좌우 입력을 구분합니다.

## 주요 구현 요소

### 이전 입력을 기억하는 교대 입력 처리

`BP_Runner`에서 `E_Input`과 `LastInput`을 이용해 이전 입력을 관리합니다. 좌우 입력을 구분하고, 교대 입력에 따른 속도 증가를 캐릭터 이동에 연결했습니다.

- 좌·우 입력 액션을 각각 처리
- 이전 입력과 현재 입력을 비교하는 구조
- `IncreaseSpeed` / `DecreaseSpeed`로 속도 변경 로직 분리
- `SpeedIncreasePerTap`과 `MaxSpeed`로 입력당 가속과 최대 속도 설정

### 곡선을 활용한 가속·감속

`C_SpeedIncreaseRamp`와 `C_SpeedDecreaseRamp`를 사용해 속도 변화를 조절합니다.

- Curve의 `GetFloatValue`로 가속·감속에 사용할 값 조회
- 입력과 시간에 따른 속도 변화를 `CharacterMovement`에 반영
- 속도 계산과 이동 입력을 연결해 달리기 동작 구성

### 속도에 연동되는 캐릭터 표현

대기와 달리기를 각각 `FB_Runner_Idle`, `FB_Runner_Run` 플립북으로 표현합니다. `SetRunPlayRate`를 통해 달리기 애니메이션의 재생 속도를 조절하고 이동 속도와 시각적 표현을 연결했습니다.

### 경기장과 결승선 구성

`BP_Field`에서 경기장 스프라이트와 결승선 요소를 묶어 관리합니다.

- `StageLength`로 경기장 길이 설정
- 경기장과 결승선 배치를 Blueprint로 구성
- `BP_Goal`의 겹침 이벤트로 결승선 도착 감지
- 결승선 스프라이트를 연결 상태에서 끊어진 상태로 전환

### 승자 정보와 게임 규칙 분리

`GM_Runner`에 `AnnounceWinner`와 승자 인덱스를 두고, 결승선에서 게임 규칙으로 결과를 전달합니다. `AnnouncedWinner` 이벤트 디스패처를 통해 결과를 알리는 구조를 구성했습니다.

## 실행 방법

**Unreal Engine 5.6**이 필요합니다.

1. 저장소를 내려받습니다.

   ```bash
   git clone https://github.com/HRI-GD/MashRunner.git
   ```

2. `MashRunner.uproject`를 Unreal Editor로 엽니다.
3. 기본 맵 `Content/Maps/MAP_Field`에서 **Play**를 누릅니다.
4. 게임 화면을 클릭한 뒤 **A / D**를 번갈아 눌러 달립니다.

## 프로젝트 구조

```text
MashRunner/
├── MashRunner.uproject
├── Config/                         프로젝트·맵 설정
├── Content/
│   ├── Blueprints/
│   │   ├── BP_Runner              입력·속도·캐릭터 애니메이션
│   │   ├── BP_Field               경기장과 결승선 배치
│   │   ├── BP_Goal                결승선 도착 판정과 연출
│   │   ├── GM_Runner              게임 규칙과 승자 정보
│   │   └── Enums/E_Input          입력 상태 구분
│   ├── Curves/                    가속·감속 곡선
│   ├── Input/                     입력 액션과 키 매핑
│   ├── Maps/                      경기장·디버그 맵
│   └── PaperAssets/               경기장·러너·결승선 리소스
└── docs/images/                   README 스크린샷
```

## 구현을 통해 다룬 내용

- 이전 입력 상태를 활용한 교대 입력 판정
- Curve를 이용한 가속·감속 값 조절
- 캐릭터 이동과 플립북 재생 속도의 연결
- 에디터 노출 변수를 이용한 경기장 구성
- 겹침 이벤트를 이용한 결승선 판정
- GameMode와 이벤트 디스패처를 통한 결과 전달

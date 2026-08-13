# Game QA Case Study

게임과 소프트웨어에서 발견한 품질 문제를 **다른 사람이 같은 결과를 재현할 수 있는 문서**로 정리합니다. 단순한 오류 모음이 아니라 테스트 설계, 영향 판단, 원인 가설, 수정 후 회귀 범위까지 보여주는 저장소입니다.

## Case Index

| ID | 대상 | 영역 | 상태 | 핵심 역량 |
|---|---|---|---|---|
| QA-001 | ONE-TAP | WebGL / Firebase Ranking | 검증 전 초안 | 플랫폼 호환성, 데이터 저장, 회귀 설계 |
| QA-002 | 준비 중 | Gameplay | 자료 수집 중 | 상태 전이, 경계값 |
| QA-003 | 준비 중 | UI / Input | 자료 수집 중 | 입력 예외, 사용성 |

## Repository Structure

```text
game-qa-case-study/
├─ README.md
├─ cases/
│  └─ QA-001-one-tap-webgl-firebase-ranking.md
├─ templates/
│  ├─ bug-report-template.md
│  └─ test-case-template.md
└─ evidence/
   └─ QA-001/              # 스크린샷, GIF, 로그; 원본 확보 후 추가
```

## Severity 기준

| 등급 | 기준 | 예시 |
|---|---|---|
| S1 Critical | 진행 불가, 데이터 손실, 전체 서비스 중단 | 게임 시작 불가, 전체 랭킹 유실 |
| S2 Major | 핵심 기능 실패, 우회가 어렵거나 영향이 큼 | 점수가 저장되지 않아 경쟁 기능 사용 불가 |
| S3 Minor | 일부 기능·표시 오류, 우회 가능 | 특정 해상도에서 버튼 일부 잘림 |
| S4 Trivial | 기능 영향이 거의 없는 표현 문제 | 맞춤법, 미세한 정렬 문제 |

## Priority 기준

| 등급 | 기준 |
|---|---|
| P0 Immediate | 배포/서비스를 막고 즉시 수정 필요 |
| P1 High | 다음 배포 전에 수정 필요 |
| P2 Normal | 계획된 주기에 수정 |
| P3 Low | 여유가 있을 때 개선 |

Severity는 사용자 영향, Priority는 수정 시급성을 뜻합니다. 둘을 같은 값으로 자동 판단하지 않습니다.

## Case 작성 원칙

- 관찰한 사실과 원인 추정을 분리합니다.
- 환경, 사전 조건, 재현 절차를 빠뜨리지 않습니다.
- 스크린샷만 두지 않고 로그·빌드·시간 정보를 함께 남깁니다.
- 수정 후 같은 절차만 반복하지 않고 인접 기능과 실패 경로도 확인합니다.
- 공개 문서에서 계정, 키, 개인 식별 정보는 제거합니다.

## Evidence Naming

```text
QA-001_webgl_chrome_before_01.png
QA-001_console_before_01.txt
QA-001_webgl_chrome_after_01.png
QA-001_regression_result.md
```

파일명만으로 사례, 환경, 수정 전후, 순서를 구분할 수 있게 합니다.



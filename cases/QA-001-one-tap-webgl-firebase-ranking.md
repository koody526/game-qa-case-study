# [QA-001] ONE-TAP WebGL 빌드에서 Firebase 랭킹 동작 불일치

> 상태: **검증 전 초안**  
> 이 문서는 알려진 키워드인 Unity/WebGL/Firebase/`RankingManager`/`FirebaseManager`를 기준으로 테스트 구조를 먼저 만든 것입니다. 실제 증상, 로그, 재현률, 수정 내용은 원본 소스와 실행 빌드를 확인한 뒤 확정해야 합니다.

## Summary

Unity Editor 또는 다른 빌드에서는 정상으로 보이던 랭킹 저장·조회 흐름이 WebGL 환경에서 다르게 동작하는 문제를 조사합니다. 정확한 실패 형태는 `저장 실패 / 조회 실패 / 초기화 지연 / UI 갱신 실패` 중 무엇인지 증거 확인이 필요합니다.

## Environment

| 항목 | 값 |
|---|---|
| Project | ONE-TAP |
| Unity Version | 확인 필요 |
| Build | WebGL, 빌드 식별자 확인 필요 |
| Hosting | itch.io 또는 배포 URL 확인 필요 |
| Browser | Chrome부터 확인, 버전 기록 필요 |
| Firebase SDK / DB | 제품과 버전 확인 필요 |
| Authentication | 익명/이메일/기타 방식 확인 필요 |

## Preconditions

1. 테스트 가능한 WebGL 빌드가 배포되어 있다.
2. Firebase 테스트 프로젝트 또는 안전한 테스트 데이터가 준비되어 있다.
3. 브라우저 개발자 도구와 Firebase 데이터 변화를 확인할 수 있다.
4. 테스트용 사용자 이름과 점수를 정한다.

## Steps to Reproduce — 확인용 초안

1. 시크릿 창 또는 캐시를 비운 브라우저에서 WebGL 빌드를 연다.
2. 게임을 시작해 점수를 획득하고 게임 종료 상태로 진입한다.
3. 랭킹 등록에 필요한 입력을 완료한다.
4. 등록 동작을 실행한다.
5. 랭킹 화면과 브라우저 콘솔·네트워크 요청을 확인한다.
6. 페이지를 새로 고친 뒤 같은 랭킹을 다시 조회한다.

## Expected Result

- 등록한 점수가 정의된 사용자 식별자와 함께 한 번만 저장된다.
- 랭킹 목록이 정렬 규칙에 따라 갱신된다.
- 페이지 새로고침 뒤에도 저장된 값이 유지된다.
- 실패 시 사용자가 이해할 수 있는 오류와 재시도 경로가 제공된다.

## Actual Result

`확인 필요 — 실제 화면, 데이터 변화, 콘솔 메시지를 그대로 기록`

## Reproduction Rate

`확인 필요 — 최소 5회 반복 후 n/5로 기록`

## Severity / Priority

- Severity: 임시 `S2 Major`
- Priority: 임시 `P1 High`
- Rationale: 랭킹이 핵심 경쟁·기록 기능이라면 사용자 성과가 저장되지 않거나 표시되지 않는 문제의 영향이 큽니다. 실제 게임의 핵심성, 우회 가능성, 데이터 손실 여부를 확인해 조정합니다.

## Evidence to Collect

- 수정 전 WebGL 화면 GIF
- 브라우저 Console 전체 오류와 발생 시각
- 관련 Network 요청의 상태 코드와 응답 요약
- Firebase Console의 수정 전/후 데이터 상태
- Unity Editor 또는 Standalone 빌드와의 비교
- `RankingManager`·`FirebaseManager` 초기화와 저장/조회 호출 순서
- 수정 전/후 커밋 링크

## Suspected Causes

아래는 가설이며 소스와 로그로 검증해야 합니다.

1. Firebase 초기화 완료 전에 `RankingManager`가 저장·조회를 요청한다.
2. WebGL에서 사용하는 Firebase SDK 또는 브라우저 설정이 다른 플랫폼과 다르다.
3. Hosting 도메인, 인증, 보안 규칙, CORS 관련 설정이 WebGL 요청을 제한한다.
4. 비동기 요청은 성공했지만 UI가 완료 이벤트 이후 다시 렌더링되지 않는다.
5. 사용자 식별자·점수 직렬화 또는 정렬 기준이 WebGL 경로에서 달라진다.

## Diagnostic Matrix

| 비교 | 목적 |
|---|---|
| Editor vs WebGL | 플랫폼 차이 분리 |
| 신규 브라우저 프로필 vs 기존 캐시 | 로컬 상태 영향 확인 |
| 익명 사용자 vs 기존 사용자 | 인증 상태 영향 확인 |
| 첫 등록 vs 중복 등록 | 중복·덮어쓰기 정책 확인 |
| 정상 네트워크 vs Offline 후 재연결 | 실패·복구 처리 확인 |
| 낮은 점수 vs 최고점·동점 | 정렬·경계값 확인 |

## Regression Test

| ID | 시나리오 | Expected Result |
|---|---|---|
| RT-001 | WebGL에서 신규 점수 등록 | 정확히 1건 저장되고 즉시 표시 |
| RT-002 | 페이지 새로고침 후 조회 | 동일한 랭킹과 정렬 유지 |
| RT-003 | 동일 사용자가 더 높은 점수 등록 | 정의된 갱신 정책대로 처리 |
| RT-004 | 동일 점수·동점 등록 | 동점 정렬 규칙이 일관됨 |
| RT-005 | 등록 직전 네트워크 차단 | 데이터 손상 없이 실패 안내 |
| RT-006 | 네트워크 복구 후 재시도 | 중복 없이 정상 등록 |
| RT-007 | Chrome 이외 지원 브라우저 | 지원 범위에서 동일 동작 |
| RT-008 | Editor/Standalone 재검사 | 기존 정상 플랫폼에 회귀 없음 |

## Definition of Done

- [ ] 실제 재현 절차와 재현률 확정
- [ ] 수정 전 증거 추가
- [ ] 원인 가설 중 하나를 로그·코드로 확인
- [ ] 수정 커밋 연결
- [ ] 회귀 테스트 결과와 수정 후 증거 추가
- [ ] 공개 문서에서 Firebase 비밀 정보 제거



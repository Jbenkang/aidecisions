# AI Decisions

**작업마다 모델과 reasoning effort를 선택하는 Codex 실행 라우터.**

사용자 요청과 하위 작업을 OpenAI Decisions API로 분류하고, 명시적인 정책으로 실행 설정을 선택한다. Codex가 실제 작업을 수행하며, 실행 결과와 검증 결과를 기록해 라우팅의 품질·비용·지연을 평가한다.

## 현재 상태

2026-10-08 기준 **설계 단계, 계획 v0.2**다. 이 저장소에는 개발 계획과 실험·아이디어 문서가 있으며, 실행 가능한 CLI, API 연동, 성능 측정 결과는 아직 없다.

## 개발 계획

| 문서 | 내용 |
| --- | --- |
| [개발 계획](docs/PLAN.md) | API·Codex 연결, 실행 정책, 오류·취소 처리, 구현 순서 |
| [보완점과 아이디어 20개](docs/IDEAS.md) | 우선순위, 필요한 근거, 채택·보류 기준 |
| [평가와 실험 설계](docs/EVALUATION.md) | 실행 결과 표, 필수 비교 기준, 검증기 평가, 8개 실험 |

개발 계획에는 다음을 정리했다.

- Decisions API의 확인된 계약과 프로젝트가 구현할 역할
- 메인 요청 및 서브에이전트의 모델·effort 선택 구조
- Codex CLI launcher와 `PreToolUse` hook의 연결 방식
- 입력 정보가 부족할 때의 재분류, 오류 처리, 제한된 재시도
- 단일 작업부터 하위 작업 라우팅까지의 구현 순서와 완료 조건
- 고정 모델, 규칙 기반 라우터와 비교하는 평가 설계

v0.2에서는 분류 생략, 정보 부족의 원인 구분, 검증 가능한 작업의 저비용 우선 실행, 요청·런타임·서비스 보고 설정의 구분, 취소·중복 실행 방지, 완료 예산 확보를 보강했다. 단순한 프로필 난이도 순서 대신 **검증된 완료당 총비용**을 기준으로 정책을 평가한다.

## 첫 구현 목표

한 개의 작업을 받아 `분류 → 실행 설정 선택 → Codex 실행 → 결과 검증·기록`을 재현 가능하게 수행한다. 그다음 서브에이전트 생성 직전에도 같은 라우팅 정책을 적용한다.

유효한 모델·effort가 이미 확정된 요청은 제약 검사 후 분류를 생략할 수 있다. 첫 실행 버전부터 취소와 부분 결과 보존을 지원하는 것을 목표로 한다.

분류 결과는 실행 설정을 정하는 신호다. 사용할 수 있는 모델, 지원하는 effort, 사용자 지정 설정, 실행 예산은 별도의 정책과 런타임 확인을 통해 결정한다.

## 참고

- [OpenAI Decisions guide](https://developers.openai.com/api/docs/guides/decisions)
- [OpenAI Decisions API reference](https://developers.openai.com/api/reference/resources/decisions/methods/create)
- [Codex hooks](https://learn.chatgpt.com/docs/hooks)

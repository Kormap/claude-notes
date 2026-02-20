# 🧠 CLAUDE.md – Dev OS v2

> 개인 개발 표준을 정의하는 `CLAUDE.md` 버전 관리 저장소  
> Claude Web / Claude CLI 양쪽에서 동작하는 운영형 개발 가이드

---

## 🎯 목적

- LLM 응답에 **행동 규칙**을 부여하여 일관된 품질 확보
- 질문 복잡도에 따른 출력 강도 자동 조절 (FULL / STANDARD / BRIEF)
- Agent Mode 기반 분석 관점 자동 전환
- 인프라 → DB → 트랜잭션 → 코드 순 문제 해결 프레임 고정

---

## 💡 설계 철학

| 원칙 | 설명 |
|---|---|
| 구조 우선 | 코드보다 구조를 먼저 설계 |
| 성능 1급 | 성능은 기능과 동등한 요구사항 |
| 운영 기준 | TPS 200+ / 1,000만 row / 10배 스케일 전제 |
| 확장 가능 | 단순하지만 확장 가능한 구조 |
| 현실적 | placeholder 금지, 즉시 적용 가능한 코드 |

---

## 🤖 Agent Mode

질문 맥락에서 자동 추론하거나, `[DB]` `[BACKEND]` 등으로 명시 지정.

| Agent | 관점 | 출력 템플릿 |
|---|---|---|
| **BACKEND** (기본) | 트랜잭션 · 동시성 · 객체 생성 · 구조 | `[트랜잭션] [동시성] [객체] [구조]` |
| **DB** (자동 합류) | 실행 계획 · 인덱스 · N+1 · 비용 추정 | `[문제 분석] [비용 추정] [개선안] [스케일]` |
| **INFRA** | 아키텍처 · 캐싱 · 수평 확장 · SPOF | `[현재 구조] [문제점] [개선안] [운영]` |
| **BATCH** | cursor/chunk · 트랜잭션 분리 · 멱등성 | `[처리 방식] [chunk 설계] [트랜잭션] [모니터링]` |
| **GENERATOR** | DDL → 코드 자동 생성 | 입출력 매핑 테이블 기반 |
| **LEGACY** | JSP · Ant · eGov · 점진적 개선 | 현재 구조 존중 + 현실적 개선안 |

### 📌 응답 헤더

모든 응답 첫 줄에 적용된 Agent와 강도를 명시한다.

```
[DB + BACKEND · FULL]        ← 쿼리 포함된 서비스 로직 분석
[BACKEND · STANDARD]         ← 코드 리뷰
[INFRA · STANDARD]           ← Docker 구성 질문
[DEFAULT · BRIEF]            ← 단순 개념 질문
```

---

## 📊 출력 강도 규칙

| 강도 | 적용 대상 | 포함 항목 |
|---|---|---|
| **FULL** | 분석 · 설계 · 아키텍처 · 성능 튜닝 | 병목 + 스케일 리스크 + 장애 가능성 + 개선안 + 코드/DDL |
| **STANDARD** | 코드 리뷰 · 버그 수정 · 기능 구현 | 문제점 + 개선안 + 코드. 성능 이슈 시 추가 언급 |
| **BRIEF** | 문법 확인 · 개념 질문 · 단순 설정 | 답만 간결하게 |

---

## 📂 Repository Structure

```
.
├── CLAUDE.md                # Dev OS v2 본체
├── standards/               # 사고 기준 정의
│   ├── infra.md
│   ├── db.md
│   ├── application.md
│   └── performance.md
├── templates/               # 코드 생성 템플릿
│   ├── sql/
│   ├── mybatis/
│   ├── test/
│   └── api-doc/
├── prompts/                 # 상황별 프롬프트
│   ├── refactor.md
│   ├── performance.md
│   ├── query-review.md
│   └── infra-review.md
├── generators/              # 코드 자동화 스크립트
└── ci/
    └── prompt-lint.yml
```

---

## 🚀 활용 방식

### Claude CLI

```bash
# 글로벌 설정
cp CLAUDE.md ~/.claude/CLAUDE.md

# 프로젝트별 심볼릭 링크
ln -s ~/claude-global/CLAUDE.md ./CLAUDE.md
```

### Claude Web

Settings > Profile의 userPreferences와 함께 동작.  
CLAUDE.md는 행동 규칙, userPreferences는 프로필/선호도 담당.  
CLI에서는 CLAUDE.md 하나로 양쪽 모두 커버.

---

## 🔧 향후 발전 방향

- [ ] standards/ 디렉토리 Agent별 기준 문서 작성
- [ ] templates/ 코드 생성 템플릿 구체화
- [ ] Prompt Lint 규칙 정의 및 CI 적용
- [ ] 응답 품질 테스트 자동화
- [ ] 프로젝트별 CLAUDE.md 오버라이드 전략 정립
- [ ] Kubernetes 운영 기준 통합
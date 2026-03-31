# review-my-review

> **내가 남긴 PR 코드 리뷰의 품질을 분석하고 개선 피드백을 제공하는 Claude Code 스킬**
>
> A Claude Code skill that analyzes the quality of your own PR code review comments and provides coaching feedback.

코드 자체를 리뷰하는 것이 아니라, **리뷰어로서 내가 남긴 코멘트**가 얼마나 명확하고 건설적인지 점검받고 싶을 때 사용합니다.

- 코멘트 유형 분류 (질문형 · 제안형 · 칭찬형 · 지적형)
- 잘한 패턴 / 개선이 필요한 패턴 분석
- 내가 놓친 중요한 관점 탐색 (버그, 설계, 테스트 등)
- 다음 리뷰를 위한 개인화된 체크리스트 제공

## 사전 조건

- [Claude Code](https://claude.ai/code) 설치
- [GitHub CLI (`gh`)](https://cli.github.com/) 설치 및 인증

```bash
gh auth login
```

> `gh`가 설치되어 있지 않거나 인증이 완료되지 않은 경우, 스킬이 실행 시 자동으로 설정 방법을 안내해 드립니다.
> 참고: [`references/gh-setup.md`](references/gh-setup.md)

## 설치

```bash
git clone https://github.com/kjyyjk/review-my-review.git ~/.claude/skills/review-my-review
```

## 사용법

Claude Code에서 PR URL과 함께 아래와 같이 말하면 스킬이 자동으로 실행됩니다.

```
https://github.com/owner/repo/pull/123 내 리뷰 어때?
```

**트리거 표현 예시**
- `내 리뷰 어때`
- `내 코멘트 좀 봐줘`
- `리뷰 잘 한 건지 모르겠어`
- `내가 남긴 리뷰 피드백 해줘`
- `내가 놓친 거 있어?`

## 출력 예시

```
## 한눈에 보기
코멘트 20개 — 질문형 8 · 제안형 9 · 칭찬형 1 · 지적형 2

> 학습 유도 중심의 질문형 리뷰어. 단순 수정 요청보다 스스로 생각하게 유도하는 방식 선호.

## 전체 리뷰 패턴
**잘한 점**
- 힌트 제공형 요구사항: 답을 주지 않고 실마리만 제공...

**개선이 필요한 패턴**
- 맥락 없는 단독 지적형: "꼭 필요한 개행인가요?" 처럼 이유 없이 끊긴 코멘트...

## 놓친 관점
**[Car.java] 빈 문자열 허용**
💬 제안 코멘트: "이름이 빈 문자열("")일 때는 어떻게 처리되나요?..."

## 다음 리뷰 체크리스트
- [ ] 짧은 지적형 코멘트를 남기기 전, 왜 문제인지 한 문장 추가했는가?
- [ ] "다른 곳도" 적용이 필요할 때 파일명/메서드명을 명시했는가?
```

## 파일 구조

```
review-my-review/
├── SKILL.md                        # 스킬 메인 정의 (4단계 워크플로우)
├── .claude/
│   └── settings.local.json         # 실행 권한 설정
├── evals/
│   └── evals.json                  # 성능 평가 케이스
└── references/
    ├── gh-api-guide.md             # GitHub API 수집 가이드
    ├── gh-setup.md                 # gh CLI 설치 및 인증 가이드
    ├── report-template.md          # 출력 형식 정의
    └── review-quality-rubric.md    # 코드 리뷰 품질 기준
```

# GitHub API 가이드

## 사전 체크

```bash
gh auth status
```
실패 시: `~/.claude/skills/review-my-review/references/gh-setup.md`를 안내하고 중단.

## 내 GitHub 계정 확인

```bash
gh api user --jq '.login'
```
이 값을 MY_LOGIN으로 저장.

## PR URL 파싱

PR URL (예: `https://github.com/owner/repo/pull/123`)에서 파싱:
```bash
OWNER=$(echo "$PR_URL" | cut -d'/' -f4)
REPO=$(echo "$PR_URL" | cut -d'/' -f5)
PR_NUM=$(echo "$PR_URL" | cut -d'/' -f7)
```

## 전체 인라인 코멘트 수집

```bash
gh api repos/$OWNER/$REPO/pulls/$PR_NUM/comments --paginate
```
**중요**: MY_LOGIN 필터는 스레드 구성 이후에 적용한다. 먼저 필터링하면 리뷰이 답글이 빠져 대화 맥락이 손실된다.

## 스레드 재구성 알고리즘

GitHub PR에서 모든 reply의 `in_reply_to_id`는 항상 **루트 코멘트 ID**를 가리킨다 (직전 답글 ID가 아님).

재구성 순서:
1. `in_reply_to_id`가 null → 루트 코멘트 (스레드 시작점)
2. 루트 ID를 키로, 같은 값을 `in_reply_to_id`로 가진 코멘트들을 그룹화
3. 각 그룹 내 코멘트를 `created_at` 오름차순 정렬 → 실제 대화 순서

결과: 내 코멘트(루트) → 리뷰이 답글들 → 내 재답변 흐름으로 묶기

### PENDING 코멘트의 스레드 맥락 파악

- PENDING 코멘트의 `in_reply_to_id` = 루트 코멘트 ID
- 같은 루트 ID를 가진 코멘트 중: MY_LOGIN이 아닌(리뷰이) + `created_at`이 PENDING보다 이전 → 그 중 가장 최근 것이 "내 PENDING이 응답하는 리뷰이 발언"
- **주제적 유사성으로 스레드를 추측하지 말 것** (오매칭의 원인)

## 리뷰 요약 코멘트 수집 (PENDING 포함)

```bash
gh api repos/$OWNER/$REPO/pulls/$PR_NUM/reviews --paginate
```
MY_LOGIN 필터링 후:
- PENDING 상태: `⏳` 표시 후 분석에 포함 (제출 전 개선 가능하므로)
- PENDING 리뷰의 인라인 코멘트 별도 수집:
  ```bash
  gh api repos/$OWNER/$REPO/pulls/$PR_NUM/reviews/$REVIEW_ID/comments
  ```

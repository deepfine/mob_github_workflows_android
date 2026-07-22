# Android reusable GitHub workflows

Android 저장소에서 공통으로 호출하는 GitHub Actions workflow 모음.

## Renovate PR 연계 이슈 생성

Renovate가 PR을 열거나 다시 열면 동일 저장소에 이슈를 생성하고, PR 본문에
`Closes #이슈번호`를 추가하는 workflow. PR 병합 시 연계 이슈의 자동 종료.

호출할 Android 저장소에 다음 workflow 추가 필요.

```yaml
# .github/workflows/renovate-linked-issue.yml
name: Create issue for Renovate PR

on:
  pull_request_target:
    types: [opened, reopened]

permissions:
  contents: read
  issues: write
  pull-requests: write

jobs:
  create-linked-issue:
    if: ${{ github.event.pull_request.user.login == 'renovate[bot]' }}
    uses: deepfine/mob_github_workflows_android/.github/workflows/create-renovate-linked-issue.yml@main
    with:
      issue_title_prefix: "[Dependency]"
      issue_labels: "dependencies,renovate"
```

`issue_labels`에 지정한 라벨 중 호출 저장소에 존재하지 않는 라벨은 경고 후 제외.
동일 PR에 대한 workflow 재실행 시 기존 이슈 재사용. 자체 호스팅 Renovate 계정 사용
시 `renovate_bot_login` 입력값 지정 필요.

`pull_request_target`에서는 PR 브랜치의 코드를 checkout하거나 실행하지 않으며, 이
workflow도 GitHub API 호출만 수행.

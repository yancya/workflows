# workflows

yancya / yancya-club / yancya-house / nantyara（4つの異なる GitHub owner）を横断して使い回す GitHub Actions **reusable workflow** 集。

## なぜこのリポジトリがあるか

reusable workflow の `uses:` 参照は、private リポジトリの場合 Access 設定が「同一 owner（個人アカウントまたは単一 organization）配下のみ」にしか許可できない仕様になっている。4つの異なる owner すべてから呼び出せるようにするには、reusable workflow を **public** リポジトリに置く必要がある。

このリポジトリのワークフロー自体に秘密情報は含まれない設計にすること（public だから）。

## 使い方

各リポジトリの `.github/workflows/` に、呼び出すだけの薄い caller workflow を置く。

```yaml
name: Auto-assign new issues
on:
  issues:
    types: [opened]
jobs:
  assign:
    uses: yancya/workflows/.github/workflows/auto-assign.yml@main
    permissions:
      issues: write
```

このリポジトリ側のロジック（`auto-assign.yml`）を更新すれば、`@main` で参照している全リポジトリの動作が自動的に変わる。

## 収録ワークフロー

- `auto-assign.yml` — issue 作成時に指定ユーザー（デフォルト `yancya`）を自動 assign する
- `add-to-board.yml` — issue 作成時に yancya の個人プロジェクト「やんちゃのプロジェクト」（[yancya/project](https://github.com/yancya/project) 参照）に自動追加し、Status を設定する。`project` スコープを持つ PAT を呼び出し元 repo の secret（例: `PROJECT_TOKEN`）として渡す必要がある（このリポジトリ自体には秘密情報を置かない設計のため）

## 新しいリポジトリへの展開

`~/.claude/scripts/broadcast-caller-workflow.sh` で、指定した caller workflow を対象 org 配下の全リポジトリに一括配置できる（yancya/life のセッションで管理）。

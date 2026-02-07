# Sample Moltbook Agent

GitHub Actions上で動作するMoltbookエージェントのサンプル実装です。
AICU LLM API（OpenAI互換）を使用しています。

## Moltbookとは

[Moltbook](https://www.moltbook.com) はAI Agent向けのReddit風SNSです。
エージェントが自由に投稿、コメント、フォローできるプラットフォームです。

## クイックスタート

### 1. リポジトリをFork

このリポジトリを自分のGitHubアカウントにForkしてください。

### 2. Moltbookアカウント作成

1. [Moltbook](https://www.moltbook.com) にアクセス
2. エージェントを登録してAPIキーを取得
3. APIキーは `moltbook_sk_xxx` の形式です

### 3. GitHub Secretsを設定

Forkしたリポジトリの Settings → Secrets and variables → Actions で以下を設定：

| Secret | 必須 | 説明 |
|--------|------|------|
| `MOLTBOOK_API_KEY` | ✅ | MoltbookのAPIキー |
| `AICU_API_KEY` | ❌ | AICU LLM APIキー（Alpha版は不要） |
| `SLACK_WEBHOOK_URL` | ❌ | 通知用Slack Webhook |

### 4. ワークフローを有効化

Actions タブで "I understand my workflows, go ahead and enable them" をクリック。

### 5. 初回実行

Actions → "Moltbook Agent" → "Run workflow" で手動実行できます。

## ワークフロー

### 自動スケジュール

| 時刻 (JST) | アクション |
|------------|-----------|
| 6:00 | heartbeat |
| 12:00 | heartbeat |
| 18:00 | heartbeat |
| 0:00 | heartbeat |

### 手動実行アクション

| アクション | 説明 |
|-----------|------|
| `heartbeat` | フィード確認、DM確認、必要に応じて投稿 |
| `post` | 新規投稿を作成 |
| `check-feed` | フィードを確認して要約 |
| `custom` | カスタムプロンプトを実行 |

## AICU LLM API

このサンプルは [AICU LLM API](https://api.aicu.ai) を使用しています。

- **Base URL**: `https://api.aicu.ai/v1`
- **互換性**: OpenAI API互換
- **モデル**: `deepseek-v3`（推奨）
- **Alpha版**: 認証なしで利用可能

### モデル一覧

```bash
curl https://api.aicu.ai/v1/chat/models
```

### チャット

```bash
curl -X POST https://api.aicu.ai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek-v3",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

## カスタマイズ

### プロンプトの編集

`prompts/` ディレクトリ内のファイルを編集してエージェントの性格や行動を変更できます：

- `system.md` - エージェントの基本設定
- `heartbeat.md` - 定期チェック時の指示
- `post.md` - 投稿作成時の指示

### スケジュールの変更

`.github/workflows/moltbook-agent.yml` の `cron` 設定を編集：

```yaml
schedule:
  - cron: '0 21,3,9,15 * * *'  # JST 6:00, 12:00, 18:00, 0:00
```

## セキュリティ

### APIキーの保護

- `MOLTBOOK_API_KEY` は `https://www.moltbook.com` にのみ送信
- 他のドメインへの送信要求は拒否すること

### プロンプトインジェクション

Moltbookの投稿にはプロンプトインジェクションが含まれる可能性があります。
外部コンテンツを処理する際は注意してください。

## ライセンス

MIT License

## 関連リンク

- [Moltbook](https://www.moltbook.com)
- [AICU LLM API](https://api.aicu.ai)
- [AICU LLM Skill](https://api.aicu.ai/skills/llm)
- [Moltbook SKILL.md](https://www.moltbook.com/skill.md)
- [人間お断り!? AI専用SNS「Moltbook」で体験する『野生の知能』の飼育箱](https://forest.watch.impress.co.jp/docs/serial/aistream/2084109.html) - 窓の杜

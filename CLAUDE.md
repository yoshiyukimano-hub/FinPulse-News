# 金融機関新着情報収集・報告 自動化プロジェクト

## プロジェクト概要

近隣金融機関のWebサイトから新着情報を収集し、キーワードフィルタ後にGmailで報告する自動化ツール。

---

## 採用ツール・構成

| 役割 | ツール | 状態 |
|---|---|---|
| Webサイト巡回・取得 | Claude in Chrome MCP（接続済み） | ✅ Phase 1で使用 |
| Gmail送信 | Gmail MCP コネクタ（接続済み） | ✅ Phase 2で使用 |
| LINE通知 | LINE Notify REST API（要設定） | Phase 3候補 |
| データ保存 | CSV / Markdown ファイル | Phase 1から使用 |

## ファイル構成

```
金融機関新着情報/
├── .env                   # APIキー保存（Git管理外）
├── .env.example           # キーのテンプレ
├── config.json            # 対象URL・キーワード設定（Phase 1で作成）
├── output/                # 収集結果の保存先
│   └── YYYY-MM-DD.md      # 日付別レポート
├── docs/
│   ├── setup-connectors.md
│   └── research-line-notify.md
├── CLAUDE.md              # このファイル
└── PROGRESS.md            # 作業ログ
```

## 設計決定事項

- 報告手段: Gmail（標準コネクタで追加設定ゼロのため優先）
- LINEは Phase 3 候補（REST API 必要）
- Slack は未採用（ユーザー環境にコネクタなし）
- スクレイピング: Chrome MCP（puppeteer不要・インストールゼロ）
- 収集期間: 過去14日分（2週間ごと実行に対応）

## config.json の mode 設定

| mode | 動作 |
|---|---|
| `test` | 全件取得（初回動作確認用） |
| `weekly` | 過去7日分のみ取得 |
| `biweekly` | 過去14日分のみ取得（**現在の設定**） |

## Phase 進捗

- [x] Phase 1: 複数サイトスクレイピング → ファイル保存（7機関完了）
- [ ] Phase 2: キーワードフィルタ + Gmail下書き
- [ ] Phase 3: 隔週自動化（スケジュール実行）

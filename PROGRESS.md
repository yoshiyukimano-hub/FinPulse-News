# 作業ログ

## 2026-05-17 — セッション1開始

- プロジェクト方針決定・ロードマップ策定
- CLAUDE.md / PROGRESS.md 作成

## 2026-05-17 — Phase 1 完了

- 対象7機関すべてのスクレイピング・フィルタ適用完了
- config.json に除外ルール設定済み（各機関ごとに個別チューニング）
- 収集結果保存: output/2026-05-17.md

### 機関別サマリー

| 機関名 | 通過件数 | 備考 |
|---|---|---|
| 帯広信用金庫 | 11件 | 正常取得 |
| 北海道銀行 | 4件（暫定） | 120件以上中の冒頭10件のみ取得 |
| 北洋銀行 | 3件（暫定） | 一覧ページ文字化けのため検索経由で取得 |
| JAおとふけ | 6件 | 燃料・ガソリン系25件を除外 |
| 十勝信用組合 | 4件 | 全99件中冒頭7件を確認 |
| JAめむろ | 5件 | 定型約款・規定類を大量除外 |
| JA木野 | 4件 | 全10件中 |

### 技術的課題（Phase 2以降で対応）

- 北洋銀行: 一覧ページ文字化け → 別途取得方法検討
- 北海道銀行: 全120件以上を週次モードで絞り込む必要あり
- 週次モード（config.json の `"mode": "weekly"` 切替）は未実装

---

## 2026-05-21 — セッション2

- PROGRESS.md を更新（Phase 1 完了記録）
- config.json の mode を `test` → `biweekly`（過去14日分）に変更
- CLAUDE.md に mode 説明表・Phase 1 完了チェックを追記
- Gmail下書き作成（Phase 2）を試みたが、今回は中止

---

## 2026-05-23 — セッション3

- Gmail下書き作成に成功（テストデータ 2026-05-17 収集分・全件モード）
  - 宛先: yoshiyuki.mano@gmail.com
  - 件名: 【金融機関新着情報】2026年5月17日収集分（7機関・計37件通過）
  - ⚠️ このデータは旧 test モード（全件）のため実運用データではない
- Anthropic API コスト試算を実施
- プログラム自動化の検討開始
- 参考リポジトリ（C:\Users\mano\src\AI-trend-weather-News）は未確認（次回共有予定）

---

## ⏭️ 次回セッションへの引き継ぎ（2026-05-23時点）

### 現状サマリー

| 項目 | 状態 |
|---|---|
| Phase 1 スクレイピング | ✅ 完了（output/2026-05-17.md・全件モード） |
| config.json mode | ✅ `biweekly`（過去14日分）に設定済み |
| Phase 2 Gmail下書き | ✅ テスト作成済み（旧データ使用・実運用データではない） |
| Phase 3 プログラム自動化 | 🔲 検討中（次回から着手） |

---

## 🤖 プログラム自動化 検討メモ

### Anthropic API コスト試算（biweekly・7機関）

| モデル | 1回あたり | 年間（26回）|
|---|---|---|
| Claude Haiku 4.5 | 約 $0.10 | 約 $2.6 |
| Claude Sonnet 4.6 | 約 $0.40 | 約 $10 |
→ **Haiku 推奨**（十分な精度）

### 自動化できる処理一覧

| 処理 | 現在 | プログラム化後 | API必要？ |
|---|---|---|---|
| 各サイト巡回・HTML取得 | Chrome MCP（手動） | Python `requests` | 不要 |
| 記事タイトル・日付の抽出 | 手動確認 | `BeautifulSoup` | 一部必要※ |
| 14日以内フィルタ | 手動 | 日付比較ロジック | 不要 |
| キーワードフィルタ | 手動 | config.json をそのまま実装 | 不要 |
| output/YYYY-MM-DD.md 保存 | 手動 | ファイル書き込み | 不要 |
| Gmail送信 | Gmail MCP（手動） | Gmail API / SMTP | 不要 |
| 隔週スケジュール実行 | 手動 | GitHub Actions cron | 不要 |

※ 北洋銀行（文字化け）など構造が複雑なサイトのHTML解析にのみ Claude API が有効

### 参考リポジトリ（AI-trend-weather-News）について

- ローカルパス: `C:\Users\mano\src\AI-trend-weather-News`
- GitHub: https://github.com/yoshiyukimano-hub/AI-trend-weather-News（非公開のため未確認）
- **次回：主要ファイルの内容をチャットに貼り付けてもらい、実装を参考にする**
  - 特に確認したいファイル: メインスクリプト、requirements.txt、Gmail送信部分

### 次回やること（優先順位順）

1. **AI-trend-weather-News のコードを共有してもらう**（Gmail送信・スクレイピング部分）
2. **Pythonスクリプトの設計・実装**
   - `scraper.py`：各機関サイトからHTML取得 → 記事抽出 → 日付フィルタ
   - `filter.py`：config.json の除外ルールを適用
   - `mailer.py`：Gmail API でメール送信
   - `main.py`：全体を統合して実行
3. **GitHub Actions で隔週自動実行を設定**（Phase 3）

### 注意事項

- 北洋銀行は一覧ページ文字化け → 直接URLアクセス or Claude API で解析
- 北海道銀行は件数が多い（120件以上）→ biweekly フィルタで14日分に絞る
- Gmail API は OAuth 2.0 の初回設定が必要（credentials.json を .gitignore に追加）
- Gmail送信後は確認してから実運用へ（自動送信はテスト後に有効化）

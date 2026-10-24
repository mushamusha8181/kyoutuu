# kyoutuu

各アプリ開発で共通して使うルール・デザイン・テンプレートの正本です。

## 役割

このリポジトリには、アプリをまたいで共通化する内容だけを置きます。

- `AGENTS.md` — どう開発するか
- `docs/DESIGN_SYSTEM.md` — どう見せるか
- `docs/KENGEN.md` — Androidアプリ開発時の権限設計・実装・確認ルール
- `templates/CURRENT_SPEC.md` — 各アプリの現在仕様テンプレート
- `templates/REGRESSION_CHECKLIST.md` — 回帰確認テンプレート
- `templates/KNOWN_ISSUES.md` — 未解決バグ記録テンプレート
- `templates/BUG_HISTORY.md` — 修正済みバグ履歴テンプレート
- `templates/DECISIONS.md` — 重要な設計判断テンプレート

## 各アプリ側に置くもの

各アプリのリポジトリでは、アプリ固有情報を次のように管理します。

```text
README.md
AGENTS.md または共通AGENTSへの参照

docs/
├─ DESIGN_SYSTEM.md または共通デザインへの参照
├─ KENGEN.md                   # Androidアプリの場合
├─ CURRENT_SPEC.md
├─ REGRESSION_CHECKLIST.md
├─ KNOWN_ISSUES.md
├─ BUG_HISTORY.md
└─ DECISIONS.md        # 必要な場合のみ
```

## 基本方針

- 共通ルールはこのリポジトリを正本にする
- Androidアプリでは実装前に `docs/KENGEN.md` を確認する
- アプリ固有仕様は各アプリの `docs/CURRENT_SPEC.md` を正本にする
- 未解決バグをCURRENT_SPECへ混ぜない
- 仕様変更時はCURRENT_SPECと回帰チェックを同時に見直す
- バグ修正時はKNOWN_ISSUESを更新し、必要な内容をBUG_HISTORYへ残す
- 作業完了前にREADMEも毎回確認する
- コード変更後は毎回、可能な範囲で回帰確認を行う

## 新しいアプリを始めるとき

`templates/` の必要なファイルを各アプリの `docs/` へコピーし、そのアプリ固有の内容だけを埋めます。

Androidアプリでは `docs/KENGEN.md` も共通ファイルとして配置します。

共通ファイルの自動同期は、対象リポジトリと運用方法を確定後にGitHub Actions等で追加します。

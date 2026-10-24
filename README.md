# kyoutuu

各アプリ開発で共通して使うルール・デザイン・テンプレートの正本です。

## 役割

このリポジトリには、アプリをまたいで共通化する内容だけを置きます。

- `AGENTS.md` — どう開発するか
- `docs/DESIGN_SYSTEM.md` — どう見せるか
- `docs/KENGEN.md` — Androidアプリ開発時の権限設計・実装・確認ルール
- `docs/VERSIONING.md` — アプリのバージョン表示・命名・更新ルール
- `docs/GIT_WORKFLOW.md` — 複数Chat・複数AIで並行開発するときのブランチ・Push・統合ルール
- `templates/README_APP.md` — 各アプリのGitHubトップに置くREADMEテンプレート
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
├─ VERSIONING.md               # 共通バージョン管理ルール
├─ GIT_WORKFLOW.md             # 並行開発・安全なPushルール
├─ CURRENT_SPEC.md
├─ REGRESSION_CHECKLIST.md
├─ KNOWN_ISSUES.md
├─ BUG_HISTORY.md
└─ DECISIONS.md        # 必要な場合のみ
```

## README の基本方針

各アプリのGitHubリポジトリ直下には `README.md` を必ず用意する。

READMEはGitHubで最初に見る人向けのアプリ説明として、最低限次が分かる状態にする。

- アプリ名
- アプリの概要
- 主な機能
- 対応環境
- 基本的な使い方
- 必要な場合は権限・端末設定
- 現在のアプリバージョン

新規アプリでは `templates/README_APP.md` を土台に、そのアプリの実態に合わせて作成する。

バージョンが `1.00 → 1.01` のように1つでも変わった場合は、メジャー更新かどうかに関係なくREADMEを同じ変更内で必ず見直し、現在バージョン表記を更新する。
その更新で概要、主要機能、使い方、対応環境、注意点などが変わった場合は、それらも現在状態へ合わせる。

READMEは仕様の正本ではない。アプリ固有の現在仕様は `docs/CURRENT_SPEC.md` を正本とする。

## 基本方針

- 共通ルールはこのリポジトリを正本にする
- Androidアプリでは実装前に `docs/KENGEN.md` を確認する
- 新規アプリ・設定画面・配布版更新では `docs/VERSIONING.md` を確認する
- 複数Chat・複数AI・複数開発者で並行作業する場合は `docs/GIT_WORKFLOW.md` に従う
- 並行作業では原則として各Chat・各作業ごとに専用ブランチを分ける
- Pushまたはmain統合の直前にリモートの最新状態を再確認する
- 通常開発では強制Pushで他作業を上書きしない
- 各アプリの設定画面から現在のバージョンを確認できるようにする
- 各アプリのGitHubリポジトリ直下に `README.md` を用意する
- バージョン変更時はREADMEも必ず同じ変更内で更新する
- アプリ固有仕様は各アプリの `docs/CURRENT_SPEC.md` を正本にする
- 未解決バグをCURRENT_SPECへ混ぜない
- 仕様変更時はCURRENT_SPECと回帰チェックを同時に見直す
- バグ修正時はKNOWN_ISSUESを更新し、必要な内容をBUG_HISTORYへ残す
- 作業完了前にREADMEも毎回確認する
- コード変更後は毎回、可能な範囲で回帰確認を行う

## 新しいアプリを始めるとき

まず `templates/README_APP.md` を土台に、リポジトリ直下の `README.md` を作成する。

そのうえで、`templates/` の必要な管理ファイルを各アプリの `docs/` へコピーし、そのアプリ固有の内容だけを埋める。

Androidアプリでは `docs/KENGEN.md` も共通ファイルとして配置する。

`docs/VERSIONING.md` も共通ルールとして配置し、設定画面のバージョン表示と更新時のバージョン管理に使用する。

`docs/GIT_WORKFLOW.md` も共通ルールとして配置し、複数Chat・複数AIで作業するときは専用ブランチを作って安全にPush・統合する。

共通ファイルの自動同期は、対象リポジトリと運用方法を確定後にGitHub Actions等で追加します。

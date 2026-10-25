# kyoutuu

各アプリ開発で共通して使うルール・デザイン・テンプレートの正本です。

## 役割

このリポジトリには、アプリをまたいで共通化する内容だけを置きます。

- `AGENTS.md` — どう開発するかの共通原則
- `docs/DESIGN_SYSTEM.md` — 共通デザインの正本
- `docs/KENGEN.md` — Android権限設計・実装・確認の正本
- `docs/VERSIONING.md` — バージョン表示・命名・更新の正本
- `docs/GIT_WORKFLOW.md` — 並行開発・ブランチ・競合・安全なPushの正本
- `docs/CI.md` — GitHub Actionsの常設と「Push」後のCI実行・結果確認の正本
- `docs/QUALITY_CHECK.md` — 品質確認の順序・完成判定の正本
- `docs/MONITORING.md` — Listener / Observer / MutationObserver等の監視ルールの正本
- `templates/android-ci.yml` — Android用GitHub Actionsテンプレート
- `templates/README_APP.md` — 各アプリのGitHubトップに置くREADMEテンプレート
- `templates/CURRENT_SPEC.md` — 各アプリの現在仕様テンプレート
- `templates/REGRESSION_CHECKLIST.md` — 回帰確認テンプレート
- `templates/KNOWN_ISSUES.md` — 未解決バグ記録テンプレート
- `templates/BUG_HISTORY.md` — 修正済みバグ履歴テンプレート
- `templates/DECISIONS.md` — 重要な設計判断テンプレート

## 正本と各アプリ側の関係

共通ルールの正本はこの `kyoutuu` とする。

同じルールを複数文書へ全文コピーして別々に管理しない。内容が重なる場合は上記の役割ごとの専門文書を正本とし、`AGENTS.md` やREADMEは原則として参照・要約に留める。

各アプリへ共通ファイルの作業用コピーを置く場合も、共通ルール自体を独自改変して別の正本を増やさない。アプリ固有の例外や仕様は各アプリの `docs/CURRENT_SPEC.md` で管理する。

各アプリでは概ね次の構成を使用する。

```text
README.md
AGENTS.md または共通AGENTSへの参照
.github/workflows/android-ci.yml    # Androidアプリ

docs/
├─ DESIGN_SYSTEM.md または共通デザインへの参照
├─ KENGEN.md                   # Androidアプリの場合
├─ VERSIONING.md
├─ GIT_WORKFLOW.md
├─ CI.md                       # Androidアプリの場合
├─ QUALITY_CHECK.md
├─ MONITORING.md
├─ CURRENT_SPEC.md
├─ REGRESSION_CHECKLIST.md
├─ KNOWN_ISSUES.md
├─ BUG_HISTORY.md
└─ DECISIONS.md                # 必要な場合のみ
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

## Push の基本方針

GitHub Actionsが常設されているリポジトリでの「Push」の扱いは `docs/CI.md` を正本とする。
ユーザーから「Push」「Pushして」「GitHubに反映して」と指示された場合は、明示的にPushだけで止める指定がない限り、Push後のActions完了・成否確認と結果報告まで行う。

Push前の最新状態確認、作業ブランチ、競合解決、強制Push禁止などのGit操作は `docs/GIT_WORKFLOW.md` に従う。

## 基本方針

- 共通ルールはこのリポジトリを正本にする
- 詳細ルールは役割ごとの専門文書を正本とし、他文書には必要以上に重複記載しない
- Androidアプリでは実装前に `docs/KENGEN.md` を確認する
- 新規アプリ・設定画面・配布版更新では `docs/VERSIONING.md` を確認する
- 新規Androidアプリでは主要機能の実装より先に `templates/android-ci.yml` を `.github/workflows/android-ci.yml` として配置し、最初にGitHubへPushしてActionsを有効化する
- GitHub Actionsが常設されている場合、「Push」の指示は `docs/CI.md` に従ってActionsの完了・成否確認まで含む
- 複数Chat・複数AI・複数開発者で並行作業する場合は `docs/GIT_WORKFLOW.md` に従う
- 通常開発では強制Pushで他作業を上書きしない
- コード変更後の品質確認・完成判定は `docs/QUALITY_CHECK.md` に従う
- 監視処理は `docs/MONITORING.md` に従い、MutationObserverを一律禁止せず安全条件を守る
- 各アプリの設定画面から現在のバージョンを確認できるようにする
- 各アプリのGitHubリポジトリ直下に `README.md` を用意する
- バージョン変更時はREADMEも必ず同じ変更内で更新する
- アプリ固有仕様は各アプリの `docs/CURRENT_SPEC.md` を正本にする
- 未解決バグをCURRENT_SPECへ混ぜない
- 仕様変更時はCURRENT_SPECと回帰チェックを同時に見直す
- バグ修正時はKNOWN_ISSUESを更新し、必要な内容をBUG_HISTORYへ残す
- 作業完了前にREADMEも毎回確認する
- コード変更後は毎回、可能な範囲で回帰確認を行う

## 新しいAndroidアプリを始めるとき

基本順序は次のとおり。

```text
リポジトリ作成
↓
共通ルール・管理ファイル配置
↓
README作成
↓
GitHub Actions配置
↓
GitHub Actionsを最初にPush
↓
Actionsが有効であることを確認
↓
主要機能の実装開始
```

`templates/README_APP.md` を土台にリポジトリ直下の `README.md` を作成する。

`templates/` の必要な管理ファイルを各アプリの `docs/` へ配置し、そのアプリ固有の内容だけを埋める。

Androidアプリでは `docs/KENGEN.md`、`docs/VERSIONING.md`、`docs/GIT_WORKFLOW.md`、`docs/CI.md`、`docs/QUALITY_CHECK.md`、`docs/MONITORING.md` を共通ルールとして利用する。

GitHub Actionsは `templates/android-ci.yml` を対象アプリへ合わせて必要最小限調整し、`.github/workflows/android-ci.yml` として配置する。

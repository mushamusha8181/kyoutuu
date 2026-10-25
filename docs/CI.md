# CI.md

## 目的

この文書は、各アプリでGitHub Actionsを常設し、PushやPull Requestのたびに最低限の自動品質確認を行うための共通ルールを定義する。

最重要原則：

> 新規Androidアプリでは、主要機能の実装を始める前にCIを用意する。

ローカルで確認し忘れてもGitHub側で異常を検出できる状態を、開発開始時から維持する。

---

## 1. 新規Androidアプリでは最初にGitHub Actionsを常設する

新しいAndroidアプリのリポジトリを作成したら、主要機能の実装より先に `.github/workflows/android-ci.yml` を配置する。

共通テンプレートは `templates/android-ci.yml` を使用し、対象アプリの構成に合わせて必要最小限だけ調整する。

CIを後付けにしない。

---

## 2. 基本トリガー

CIは少なくとも次で実行する。

- デフォルトブランチ（`main` / `master` 等）へのPush
- デフォルトブランチ向けPull Request

対象リポジトリの実際のデフォルトブランチに合わせてWorkflowの `branches` を確認する。

---

## 3. Androidの基本タスク

Androidアプリでは、原則として次を自動実行する。

1. `testDebugUnitTest`
2. `lintDebug`
3. `assembleDebug`
4. `regressionCheck` が定義されている場合は `regressionCheck`

`regressionCheck` はAndroid / Gradleの標準タスクではないため、存在しないアプリで無理に実行してCIを壊さない。

アプリ側に回帰用Gradleタスクを用意した場合は、自動的にCIへ組み込む。

---

## 4. Lintレポートを必ず保存する

`lintDebug` の結果は、GitHub ActionsのArtifactとして保存する。

Lintが失敗した場合でも原因を確認できるよう、Artifactアップロードは `if: always()` で実行する。

保存対象は、生成される範囲で次を含める。

- HTML
- XML
- TXT

例：

```text
**/build/reports/lint-results-*.html
**/build/reports/lint-results-*.xml
**/build/reports/lint-results-*.txt
```

Lintの警告件数が0でないだけで機械的に成功扱い・失敗扱いを決めず、重大度と新規増加を `docs/QUALITY_CHECK.md` に従って確認する。

---

## 5. 失敗後も診断情報を残す

テストやLintが失敗しても、後続の診断やArtifact保存が可能な範囲で実行されるようにする。

ただし、失敗を無視してWorkflow全体を成功扱いにはしない。

CI失敗は「確認すべき異常がある」という合図として扱う。

---

## 6. CIとローカル確認の役割

GitHub Actionsはローカル確認の代替ではない。

ローカルでは `docs/QUALITY_CHECK.md` に従って品質確認を行い、GitHub ActionsではPush / Pull Request後の自動安全網として同じ重要項目を再確認する。

実機確認、メーカー差、権限ダイアログ、端末固有挙動など、CIだけでは確認できない項目を「CIが通ったから確認済み」と扱わない。

---

## 7. CIを壊したまま放置しない

Workflowが継続的に失敗している状態を通常状態にしない。

失敗した場合は、少なくとも次を確認する。

- 今回のコード変更による失敗か
- Workflow自体の設定ミスか
- Gradle / JDK / SDK等の環境差か
- 既存から継続している失敗か
- `regressionCheck` 等の独自タスクが存在するか

テストやLintを無効化してCIを緑にするだけの修正を行わない。

---

## 8. 完成・統合時の扱い

PushまたはPull Requestを行った場合は、利用可能な範囲でGitHub Actionsの結果を確認する。

CIが未実行・失敗・確認不能の場合は、その事実を明示する。

CIが成功しても、`docs/QUALITY_CHECK.md` の実機確認など未自動化項目を省略しない。

---

## 最終原則

新規Androidアプリでは、

```text
リポジトリ作成
↓
共通ルール配置
↓
GitHub Actions常設
↓
主要機能の実装開始
```

を基本順序とする。

> CIは後から足す保険ではなく、開発開始時から常設する安全網とする。

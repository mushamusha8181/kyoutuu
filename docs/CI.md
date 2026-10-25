# CI.md

## 目的

この文書は、各アプリでGitHub Actionsを常設し、PushやPull Requestのたびに最低限の自動品質確認を行うための共通ルールを定義する。

この文書を、**「Push」という指示にどこまで含めるか、GitHub Actionsをどう実行・確認するかの正本**とする。
ブランチ・競合・強制Push禁止などのGit操作そのものは `docs/GIT_WORKFLOW.md`、品質確認全体の順序と完成判定は `docs/QUALITY_CHECK.md` を正本とする。

最重要原則：

> 新規Androidアプリでは、主要機能の実装を始める前にCIを用意してGitHubへPushし、以後「Push」はActionsの実行・結果確認までを含む。

ローカルで確認し忘れてもGitHub側で異常を検出できる状態を、開発開始時から維持する。

---

## 1. 新規Androidアプリでは最初にGitHub Actionsを常設する

新しいAndroidアプリのリポジトリを作成したら、主要機能の実装より先に `.github/workflows/android-ci.yml` を配置する。

共通テンプレートは `templates/android-ci.yml` を使用し、対象アプリの構成に合わせて必要最小限だけ調整する。

Workflowをローカルに置くだけで先へ進まず、原則として最初のPushでGitHubへ反映し、Actionsが実際に有効になったことを確認してから主要機能の実装へ進む。

CIを後付けにしない。

---

## 2. 「Push」はActions確認までを含む

ユーザーから「Push」「Pushして」「GitHubに反映して」など、GitHubへ変更を送る指示があった場合は、明示的にPushだけで止めるよう指定されていない限り、次を1セットとして扱う。

```text
最新リモート状態を確認
↓
安全にPush
↓
そのPushに対応するGitHub Actionsを実行・起動確認
↓
Actionsの完了を確認
↓
成功 / 失敗 / 未実行を確認
↓
結果をユーザーへ報告
```

Actionsを回すためだけの追加確認は原則として求めない。

Push自体が成功しても、Actionsが失敗している場合は「完了」「問題なし」と扱わない。Push結果とCI結果を分けて報告する。

Workflowが自動起動しない場合は、`workflow_dispatch` が利用できるなら同じコミット・ブランチを対象に実行する。

Actionsが無効、権限不足、接続不可、Workflow不在などで実行・確認できない場合は、その事実を明示する。

---

## 3. 基本トリガー

CIは原則として次で実行する。

- すべてのブランチへのPush
- Pull Request
- 必要に応じた手動実行 `workflow_dispatch`

これにより、デフォルトブランチだけでなく各Chat・各作業ブランチへPushした時点でも自動チェックを行う。

リポジトリ固有の事情で対象を絞る場合は、Push指示時に対象ブランチのCIが実際に実行されることを確認する。

---

## 4. Androidの基本タスク

品質確認全体の順序は `docs/QUALITY_CHECK.md` を正本とする。
GitHub Actionsでは、そのうち自動化できる基本ゲートとして原則次をこの順序で実行する。

1. `assembleDebug`
2. `lintDebug`
3. `testDebugUnitTest`

加えて、`regressionCheck` が定義されている場合は自動回帰確認として実行する。

`regressionCheck` はAndroid / Gradleの標準タスクではないため、存在しないアプリで無理に実行してCIを壊さない。

クラッシュ / ANR、メモリ、権限 / セキュリティ、実機確認など、CIだけで十分に確認できない項目は `docs/QUALITY_CHECK.md` に従って別途確認する。

---

## 5. Lintレポートを必ず保存する

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

Push後の結果報告では、必要に応じてLintレポートArtifactが保存されたことも確認する。

---

## 6. 失敗後も診断情報を残す

ビルド、Lint、テスト等が失敗しても、後続の診断やArtifact保存が可能な範囲で実行されるようにする。

ただし、失敗を無視してWorkflow全体を成功扱いにはしない。

CI失敗は「確認すべき異常がある」という合図として扱う。

失敗時は可能な範囲で失敗Job・Step・ログを確認し、どこで失敗したかを報告する。

---

## 7. CIとローカル確認の役割

GitHub Actionsはローカル確認の代替ではない。

ローカルでは `docs/QUALITY_CHECK.md` に従って品質確認を行い、GitHub ActionsではPush / Pull Request後の自動安全網として重要項目を再確認する。

実機確認、メーカー差、権限ダイアログ、端末固有挙動など、CIだけでは確認できない項目を「CIが通ったから確認済み」と扱わない。

---

## 8. CIを壊したまま放置しない

Workflowが継続的に失敗している状態を通常状態にしない。

失敗した場合は、少なくとも次を確認する。

- 今回のコード変更による失敗か
- Workflow自体の設定ミスか
- Gradle / JDK / SDK等の環境差か
- 既存から継続している失敗か
- `regressionCheck` 等の独自タスクが存在するか

テストやLintを無効化してCIを緑にするだけの修正を行わない。

---

## 9. 完成・統合時の扱い

PushまたはPull Requestを行った場合は、利用可能な範囲でGitHub Actionsの完了結果まで確認する。

作業ブランチをPushした場合はそのブランチのCIを確認し、デフォルトブランチへ統合した場合は統合後のコミットに対するCIも確認する。

CIが未実行・失敗・確認不能の場合は、その事実を明示する。

CIが成功しても、`docs/QUALITY_CHECK.md` の未自動化項目を省略しない。

---

## 最終原則

新規Androidアプリでは、

```text
リポジトリ作成
↓
共通ルール配置
↓
GitHub Actions配置
↓
GitHub Actionsを最初にPush
↓
Actionsが有効であることを確認
↓
主要機能の実装開始
```

を基本順序とする。

通常作業では、

```text
「Pushして」
↓
安全にPush
↓
GitHub Actions
↓
完了結果確認
↓
結果報告
```

までを同じ依頼として扱う。

> CIは後から足す保険ではなく、開発開始時からGitHub上で稼働させる安全網とする。

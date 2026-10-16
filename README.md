# renovate-config

個人のリポジトリで共通に使う Renovate の設定（[shareable config preset](https://docs.renovatebot.com/config-presets/)）。

## 使い方

各リポジトリの `renovate.json5` で `extends` に指定する。

```json5
{
  $schema: 'https://docs.renovatebot.com/renovate-schema.json',
  extends: ['github>greendrop/renovate-config:default.json5'],

  // リポジトリごとの設定（enabledManagers / packageRules など）
  enabledManagers: ['github-actions', 'mise']
}
```

JSON5 のプリセットはファイル名まで指定する必要がある（`github>greendrop/renovate-config` だけだと `default.json` を探す）。

## 共通設定（`default.json5`）

| 設定 | 値 |
| --- | --- |
| `extends` | `config:recommended` |
| `minimumReleaseAge` | `7 days` |
| `timezone` / `schedule` | `Asia/Tokyo` / 毎日 9 時台 |
| `labels` | `dependencies` |
| `rebaseWhen` | `never` |
| `packageRules` | マネージャごとのラベル（`github-actions` → `github-actions`、`mise` → `mise`、`npm` → `javascript`、`terraform` → `terraform`） |

リポジトリ側で同じキーを指定すると上書きされる（`labels` は置き換え。追加したいときは `addLabels` を使う）。
`packageRules` はリポジトリ側の指定がプリセットの後ろに追加されるので、共通のラベル付けは残ったまま個別のルールを足せる。

## 開発

ツール（actionlint・ghalint・zizmor・yamlfmt・betterleaks・lefthook）は [mise](https://mise.jdx.dev/) でバージョンを固定している。

```bash
# lefthook install も自動実行される
mise install
```

PR では次のワークフローが動く。

| ワークフロー | 処理 |
| --- | --- |
| `secret-scan` | betterleaks で差分コミットから秘密情報を検出 |
| `renovate-config-validate` | renovate-config-validator で `renovate.json5` / `default.json5` を検証 |
| `yaml-lint` | yamlfmt で YAML を lint |
| `github-actions-lint` | actionlint・ghalint・zizmor でワークフローを lint |

このリポジトリ自体の依存（GitHub Actions・mise のツール）は `renovate.json5` で更新する。Renovate の PR には `deps-ai-review:pending` ラベルが付き、`deps-ai-review` ワークフローで AI エージェントがレビューする（仕組みは [greendrop/gha-workflows](https://github.com/greendrop/gha-workflows) の README を参照）。minor / patch は pass なら自動マージし（Actions variable `DEPS_AI_REVIEW_AUTO_MERGE` が `true` のとき）、major は `deps-ai-review:manual-merge` が付いて手動でマージする。

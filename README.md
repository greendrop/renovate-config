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
| `packageRules` | マネージャごとのラベル（`github-actions` → `github-actions`、`mise` → `mise`、`npm` → `javascript`） |

リポジトリ側で同じキーを指定すると上書きされる（`labels` は置き換え。追加したいときは `addLabels` を使う）。
`packageRules` はリポジトリ側の指定がプリセットの後ろに追加されるので、共通のラベル付けは残ったまま個別のルールを足せる。

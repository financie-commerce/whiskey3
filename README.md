# Whiskey&Co. LP — key3 マーケット表示

在る宵LPと同じ GitHub Actions + GitHub Pages 構成で、
Whiskey&Co.（key3トークン）のリアルタイムマーケット価格を表示するLPモックです。

## ファイル構成

```
.
├── .github/
│   └── workflows/
│       └── fetch-market.yml   # 10分ごとに価格を取得してmarket.jsonに書き込む
├── index.html                 # LP本体（market.jsonをfetchして表示）
├── market.json                # Actions が自動更新するデータファイル
└── README.md
```

## セットアップ手順

### 1. ownercard_id を調べる

ブラウザで https://financie.jp/communities/whiskey_and_co/market を開き、
DevTools → Network タブで `bancor` を含むリクエストを探す。

URLパターン:
```
/api/charts/bancor/{ownercard_id}/1d
```

この `{ownercard_id}` をコピーしておく（例: `0xWHISKEY0123456`）。

### 2. GitHub Secrets に登録

リポジトリ Settings → Secrets and variables → Actions → New repository secret

| Name | Value |
|------|-------|
| `OWNERCARD_ID` | 手順1で取得した ownercard_id |

### 3. GitHub Pages を有効化

Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / `/ (root)`

### 4. 初回 Actions を手動実行

Actions タブ → "Fetch Whiskey&Co. Market Data" → Run workflow

market.json が更新されたら完成。
以降は10分ごとに自動実行されます。

### 5. PROXY_URL の更新（在る宵方式を使う場合）

在る宵と同様にプロキシ経由でCORSを回避する場合は、
`index.html` 内の `MARKET_JSON_URL` を本番 GitHub Pages URLに変更する：

```js
const MARKET_JSON_URL = "https://{username}.github.io/{repo}/market.json";
```

## API仕様メモ

```
GET https://financie.jp/api/charts/bancor/{ownercard_id}/1d
```

レスポンス例:
```json
{
  "prices": [
    [1718000000000, 33.3],
    ...
  ]
}
```

`prices[n][0]` = Unix timestamp (ms), `prices[n][1]` = 価格（円）

## 在る宵LPとの差分

| 項目 | 在る宵 | Whiskey&Co. |
|------|--------|-------------|
| ownercard_id | `0xOWNERCARD180124` | Secrets経由 |
| テーマ | 和の薄暗いトーン | ウイスキー琥珀・ダーク |
| APIエンドポイント | 同一パターン | 同一パターン |
| market.json構造 | 同一 | 同一 |

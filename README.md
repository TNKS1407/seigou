# SEIGOU — 複素インピーダンスを否定しないRFマッチングアプリ

## デプロイ経路

**公開URL**: `seigou.tnks1407.com`

```
ブラウザ
  └→ Cloudflare (DNS: seigou.tnks1407.com CNAME → tunnel)
      └→ cloudflared tunnel (aa8127f8)
          └→ App Manager (localhost:3000)
              └→ static files: C:\Users\tnks0\sites\seigou\
```

**Cloudflare Pages ではない**。ローカルの App Manager が `sites/seigou/` を静的配信している。

### 修正の反映方法

1. `C:\Users\tnks0\sites\seigou\index.html` を編集する
2. `git add -A && git commit && git push` でGitHubに同期（任意）
3. 自動で公開URL に反映される（App Manager がリクエスト毎にファイルを読む）

ビルドステップなし。リロードのみで反映。

## ファイル構成

```
index.html      — アプリ本体（HTML/CSS/JS一体型）
profiles.json   — プロフィールデータ（外部JSON、fetch で読込）
README.md       — このファイル
```

## プロフィール追加

`profiles.json` に以下の形式でオブジェクトを追加するだけ：

```json
{
  "name": "名前",
  "age": 28,
  "job": "職種",
  "avatar": "👤",
  "impedance": "50Ω系",
  "q": 75,
  "gamma": 0.10,
  "vswr": 1.22,
  "freq": "2.4 GHz",
  "phase": "-5°",
  "returnLoss": "20.0 dB",
  "tags": ["タグ1", "タグ2"],
  "bio": "自己紹介文",
  "wants": ["希望1", "希望2", "希望3"],
  "g1": "#4f46e5",
  "g2": "#06b6d4"
}
```

## ユーザー登録プロフィール

「+ 登録」ボタンから作成したプロフィールはブラウザの `localStorage` に保存される。
サーバー側には保存されない。デバイスをまたいで同期されない。

## 状態管理

グローバルな `state` オブジェクトで一元管理：

```js
state = {
  deck,           // shuffled index array
  deckPos,        // current position
  highQ,          // Q filter toggle
  matches,        // match history
  currentTab,     // profile | analyzer | matches
  lastMatchedProfile,
}
```

デッキはロード時と登録時にFisher-Yatesシャッフルされる。

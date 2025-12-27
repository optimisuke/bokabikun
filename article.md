# えっ！？まだ他人が作ったタスク管理アプリ使ってんの！？

## はじめに

えっ！？まだ他人が作ったタスク管理アプリ使ってんの！？
AI Coding Agent がある今なら、あんたの好みに合わせてサッと自作できるで。
デプロイや運用がしんどいのんはわかるけど、タスク管理アプリくらいならバックエンド作らずにフロントエンドだけで動くようにして公開して使ったらむっちゃラクやで。
自分の気に入った機能だけ作ったらええし。
自分用ならログインも不要やし、公開せんでも困らへんしな。
公開も、**GitHub Pages** なら楽勝やで。
しかも AI は Tailwind を理解してるから、見た目もええ感じに仕上げやすいねんで。まさに追い風やんなぁ！

## そもそも何が面倒なん？

正直、ここが面倒なんよね。

- デプロイ
- 運用
- セキュリティや個人情報の配慮

## じゃあどうすんねん

答えはシンプルやで。
**PWA + localStorage** で、フロントエンドだけで完結したらええねん。
これで「アプリ感」と「保存」はちゃんとできるで。
**GitHub Pages**で公開できるしな。

## 実現方法

やることはこの 3 ファイルだけやで。

- `index.html`
  UI とロジックを 1 枚にまとめる。タスクの追加/更新/表示をここで完結。
- `manifest.webmanifest`
  アプリアイコンや表示名を定義して、PWA として“アプリっぽく”見せる。
- `sw.js`
  主要ファイルをキャッシュしてオフラインでも動くようにする。

### コアコード（抜粋）

こんな感じでいけるで。

`index.html`（UI・保存）

```html
<script>
  const STORAGE_KEY = "bokabikun.tasks.v1";
  const loadTasks = () => JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
  const saveTasks = (tasks) =>
    localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks));

  function markDone(id) {
    const tasks = loadTasks();
    const t = tasks.find((x) => x.id === id);
    if (!t) return;
    t.lastDone = new Date().toISOString().slice(0, 10);
    t.updatedAt = new Date().toISOString();
    saveTasks(tasks);
  }
</script>
```

アイコンも AI がつくってくれるで。
`manifest.webmanifest`

```json
{
  "name": "bokabikun",
  "short_name": "bokabikun",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#0f172a",
  "theme_color": "#0f172a",
  "icons": [{ "src": "icon.svg", "sizes": "512x512", "type": "image/svg+xml" }]
}
```

`sw.js`（事前キャッシュ）

```js
const CACHE_NAME = "bokabikun-v1";
const PRECACHE_URLS = [
  "./",
  "./index.html",
  "./manifest.webmanifest",
  "./icon.svg",
];

self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => cache.addAll(PRECACHE_URLS))
  );
});
```

### 公開方法

こことかみたらええで。むっちゃ簡単やで。

https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site

### AI に頼む時

最初は、「index.html だけでアプリ作りたい。PWA にしたい。」って伝えたらええで。

## メリット

使ってみると、ここが気持ちええで。

- GitHub Pages でデプロイできて気楽やで
- バックエンドないからセキュリティ/プライバシーリスク気にしんくていいで
- ログインやサーバ運用が不要やで

## 注意点

もちろん弱点もあるで。自分用ならエクスポートつけたりしたらどうにかなるで。

- 別端末への移行めんどいで
- ブラウザのデータ削除で消えるで

## まとめ

AI Coding Agent 時代にちょっとしたアプリ自作するなら、この構成がいいんちゃう？
タスク管理は外部連携いらんから試しやすいで。
まずは PWA + localStorage で作って、自分で使うのがええで。
UI も気に入らんくなったら、AI に修正してもらったらええしな。

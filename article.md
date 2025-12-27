# まだ他人が作ったアプリ使ってるの？AI で爆速自作、公開してノーメンテ

## はじめに

AI Coding Agent で誰でもアプリを作れる時代になった。
でも現実は、デプロイや運用が地味に重い。
だからこそ、**ネットに繋がなくて成立するアプリ**に振り切るのがラク。

タスク管理はまさにその代表。
自分用ならログインも不要だし、公開もしなくていい。
もし公開するなら **GitHub Pages** が一番楽。
AI は Tailwind を理解していて、おしゃれな見た目にしてくれるのも追い風。

## 何が面倒か

- デプロイと運用
- 公開時のセキュリティや個人情報の配慮
- 小さなアプリでもやることが多い

## じゃあどうするか

**PWA + localStorage** で完結させる。
これで「インストール感」と「保存」は確保できる。

## 実現方法（コアだけ）

- `index.html`
  UI とロジックを 1 枚にまとめる。タスクの追加/更新/表示をここで完結。
- `manifest.webmanifest`
  アプリアイコンや表示名を定義して、PWA として“アプリっぽく”見せる。
- `sw.js`
  主要ファイルをキャッシュしてオフラインでも動くようにする。

### コアコード（抜粋）

`index.html`（保存・読み込みと更新）

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

`manifest.webmanifest`（最小構成）

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

## メリット

- デプロイ不要・公開不要で気楽
- セキュリティ/プライバシーリスクが低い
- ログインやサーバ運用が不要

## 注意点

- 端末依存（別端末への移行が弱い）
- ブラウザのデータ削除で消える可能性

## まとめ

AI Coding Agent 時代は「自作 × ローカル完結」が現実的。
タスク管理は外部連携が必須じゃない。
まずは PWA + localStorage で作って、自分で使うのが一番早い。

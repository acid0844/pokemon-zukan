# 🐾 ポケモン図鑑アプリ（Vue.js + PokeAPI）

## 📌 概要

Vue 3（Composition API）と PokeAPI を用いて構築したSPA（シングルページアプリケーション）です。  
APIから取得したポケモンデータをもとに、名前検索・タイプ別絞り込み・詳細表示・画像表示など、図鑑風のUIで表示しています。

## 🛠 使用技術

- Vue.js 3（Composition API）
- Axios
- PokeAPI
- SCSS（Sass）
- LocalStorage
- Git / GitHub / Vercel

## 🧩 主な機能

- 🔍 名前でのリアルタイム検索
- 🔘 タイプ別絞り込み（日本語表示対応）
- 🖼 ポケモン画像の表示
- 📖 クリックでモーダル詳細表示（身長・体重など）
- 💛 選択中ポケモンの強調表示
- 📱 レスポンシブ対応

## 🎨 工夫したポイント

- APIから取得した英語データを日本語に変換し表示
- Composition APIを活用し、状態管理とロジックの分離を意識
- UIとUXを意識して、リスト・詳細・検索が並ぶ設計に
- コンポーネント分割による保守性向上
- ChatGPTを活用しながらエラー解消・改善を自走で繰り返し実装

## 🔗 公開デモ

👉 [https://pokemon-zukan-ud28.vercel.app/]

## 🗂 セットアップ方法（ローカル開発用）

```bash
npm install
npm run serve

# my-vue-app

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

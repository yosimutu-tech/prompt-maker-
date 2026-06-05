---
name: seminar-doc-builder-0603
description: |
  セミナーの企画書と参加者向け案内ページを2ファイル生成するスキル。
  以下のワードで発火する（他スキルとの誤発火を避けるため、セミナー文書生成に絞る）：
  - 「セミナーの企画と案内資料」
  - 「セミナー企画書」「セミナー案内ページ」を同時に生成・作成したいとき
  - 「kikakusho.html」「annai.html」を作ってほしいとき
  - 「企画書と案内ページ」「運営資料 2点」のような組み合わせ要求
  発火しないケース：セミナー内容の相談のみ・1ファイルだけの依頼・HTMLと無関係な企画書
---

# セミナー運営資料 2点生成スキル（seminar-doc-builder-0603）

ユーザーが指定したテーマで、社内向け企画書 `kikakusho.html` と対外向け案内ページ `annai.html` を生成する。
デザインシステム・構成・コーディングルールを完全に再現すること。

## このスキルの実行フロー

作業は次の順序で行う。

1. テーマ・主催者名などの情報を確認（ユーザーメッセージから読み取れない場合のみ質問）
2. `kikakusho.html` を作成
3. `annai.html` を作成
4. git add / commit / push
5. 完了サマリーを出力

---

## デザインシステム（変えない）

| 項目 | 値 |
|------|-----|
| ベースカラー | 紺 `#16365C` |
| アクセントカラー | 山吹 `#F4A300` |
| 背景色 | 生成り `#FBF8F1` |
| カード | 角丸 16px、`box-shadow: 0 2px 12px rgba(22,54,92,.08)` |
| フォント | `"Hiragino Kaku Gothic ProN", "Noto Sans JP", "Meiryo", sans-serif` |
| セクション見出し | 左に連番丸バッジ（山吹背景・白文字・36px円）＋ `h2` |
| フッター署名 | `Produced with Claude Code ✦ 主催：（主催者名）` |

### 丸バッジ HTML パターン
```html
<div class="sec-title">
  <div class="badge">1</div>
  <h2>セクションタイトル</h2>
</div>
```

### 共通 CSS スニペット（両ファイルに必ず含める）
```css
.badge {
  flex-shrink: 0; width: 36px; height: 36px;
  background: #F4A300; color: #fff;
  font-size: 15px; font-weight: 800; border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
}
.sec-title { display: flex; align-items: center; gap: 14px; margin-bottom: 24px; }
.sec-title h2 { font-size: 19px; font-weight: 800; color: #16365C; }
.card {
  background: #fff; border-radius: 16px;
  box-shadow: 0 2px 12px rgba(22,54,92,.08); padding: 32px 36px;
}
```

---

## kikakusho.html の構成（7ブロック・この順序厳守）

### ブロック 1 ─ タイトルと1行キャッチコピー
- `<header>` を紺背景・白文字・中央寄せ
- 山吹色の小ラベル（例：「社内企画書 ─ 2026年度」）を h1 上に配置
- h1：セミナー正式名称
- キャッチコピー（1行）：`color: #c8d8ee`

### ブロック 2 ─ 開催概要
2列テーブル（`<th>` 幅34%）。以下の行を含める：
日時 ／ 開催形式 ／ 定員 ／ 参加費 ／ 対象 ／ 主催

金額は必ず `1,500円` 形式（カンマ区切り＋「円」）。

### ブロック 3 ─ 背景・なぜ今やるのか
2〜3文のテキストのみ。市場背景・ニーズ・本セミナーの意義。

### ブロック 4 ─ 受講後のゴール（BEFORE → AFTER）
```
[BEFORE カラム]  →（矢印・山吹・font-size:36px）  [AFTER カラム]
```
- グリッド：`grid-template-columns: 1fr auto 1fr`
- BEFORE: `background:#f0ece3; border:2px solid #d6cfc3`、リスト各行の先頭に `✗`（赤）
- AFTER: `background:#eef4fb; border:2px solid #9db9d8`、リスト各行の先頭に `✓`（緑）
- 各カラムに `BEFORE` / `AFTER` の小ラベルを上部に配置

### ブロック 5 ─ プログラム
3列テーブル：時刻 ／ 内容 ／ 所要  
- 時刻列：`font-weight:700; color:#16365C`
- 所要列：`text-align:center; color:#666; font-size:13px`

### ブロック 6 ─ こんな方におすすめ
チェックリスト3項目。山吹円バッジ（✔）付き `<ul>`：
```css
.checklist li::before {
  content: "✔"; width:26px; height:26px;
  background:#F4A300; color:#fff; border-radius:50%;
  display:flex; align-items:center; justify-content:center;
}
```

### ブロック 7 ─ 持ち物・注意事項
2カラムグリッド（`grid-template-columns: 1fr 1fr`）。
左：「当日ご用意いただくもの」／ 右：「注意事項」
各ボックス：`background:#f7f4ed; border-radius:10px`、h3 下に山吹 2px ボーダー。

---

## annai.html の構成（4セクション）

### セクション A ─ ヒーロー
```css
background: linear-gradient(135deg, #16365C 0%, #1e5090 55%, #F4A300 100%);
```
- 小ラベル（「オンライン開催 ／ 初心者歓迎」など）：半透明ボーダーのカプセル
- h1：大見出し（テーマ名）。キーワード部分を `<em>` で `color:#FFD05A`
- 日時バッジ：`background:rgba(255,255,255,.15)` のカプセル＋両端に山吹ドット
- 申込ボタン（山吹・角丸100px・`box-shadow`）
- 参加費・定員の補足テキスト：`color:rgba(255,255,255,.7)`

### セクション B ─ 3つのポイント
3カラムグリッド（`grid-template-columns: repeat(3,1fr)`）。各カードに：
- 絵文字（テーマに合ったもの・42px）
- h3（ポイントタイトル）
- p（説明文）

### セクション C ─ FAQ（Q&Aカード 3問）
Q：山吹丸バッジ `Q`  
A：紺丸バッジ `A`  
各QAセット を `background:#FBF8F1; border-radius:14px; border:1px solid #e8e2d6` のカードで囲む。

```html
<div class="faq-item">
  <div class="faq-q">
    <div class="q-badge">Q</div>
    <div class="q-text">質問文</div>
  </div>
  <div class="faq-a">
    <div class="a-icon">A</div>
    <div class="a-text">回答文</div>
  </div>
</div>
```

### セクション D ─ 申込み導線
紺（`#16365C`）背景セクション。
- h2：クロージングメッセージ（キーワードを `<em color:#FFD05A>` で強調）
- p：サポートテキスト（`color:#a8bdd4`）
- 申込ボタン（山吹・ヒーローと同スタイル）
- 参加費・定員テキスト（`color:#7a9ab8`、金額を `<strong color:#F4A300>`）

---

## コーディングルール

- 各ファイルは `<!DOCTYPE html>` から始まる1枚完結HTML
- CSSは `<style>` 内にすべて内包。外部CSS / 外部JS / CDN / 画像URL は一切使わない
- レスポンシブ対応：`@media (max-width:660px)` で3カラム→1カラム等
- 金額表記：必ず `1,500円`（カンマ区切り＋「円」）
- ファイル名は必ず `kikakusho.html` と `annai.html`（上書き可）
- ファイルはダブルクリックでそのままブラウザ表示できる状態にする

---

## コンテンツ生成ガイドライン

ユーザーがテーマだけ指定した場合、以下はAIが自然な値を設定してよい：
- 開催日時・定員・参加費（テーマに合った現実的な値）
- 主催者名（架空でよい）
- プログラムの時刻と内容
- BEFORE/AFTERの項目
- FAQの質問と回答
- 3つのポイントの絵文字・タイトル・説明文

---

## 完了サマリー形式

生成とプッシュが完了したら以下を出力する：

```
## 完了

| ファイル | 内容 |
|----------|------|
| kikakusho.html | 社内企画書（7ブロック） |
| annai.html | 参加者向け案内ページ（4セクション） |

ブランチ `<branch名>` にプッシュしました。
ブラウザで直接開いてご確認ください。
```

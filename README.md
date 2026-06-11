# 東京オンラインメディカル｜ページ改修案 HTML 引き継ぎ資料

対象: オンライン診療ページ／メンズヘルス診療ページ
テーマ: clinic_2025（WordPress / ACF）
作成日: 2026-06-11

---

## 1. これは何か

現行サイト（テーマ `clinic_2025`）の **デザインをそのまま使い**、DMMオンラインクリニックを参考にコンテンツを改修した
**2ページのHTML改修案** です。WordPress本番には未反映で、本ZIPは実装担当への引き継ぎ用です。

| ファイル | 対応ページ | 元テンプレート |
|---|---|---|
| `service.html` | オンライン診療 | `singles/single-service.php` |
| `menshealth.html` | メンズヘルス診療（男性更年期・EDなど） | `singles/single-onlinecare.php`（投稿ID 170 / slug …-11） |

## 2. プレビュー方法

`index.html` をブラウザで開く（または `service.html` / `menshealth.html` を直接開く）。
ビルド不要・サーバー不要で、そのまま表示できます。

## 3. ファイル構成

```
.
├── README.md                 ← この資料
├── index.html                ← 2ページへのリンク一覧（プレビュー入口）
├── service.html              ← オンライン診療ページ 改修案
├── menshealth.html           ← メンズヘルス診療ページ 改修案
└── assets/
    ├── main.css              ← テーマ本番のコンパイル済みCSS【参照用・上書き禁止】
    ├── add-serviceDetail.css ← ★今回の追加CSS（実装対象）
    ├── images/               ← テーマ素材画像（clinic_2025/assets/images 相当）
    └── wp-content/uploads/    ← 本番uploadsの画像（カードサムネイル等）
```

## 4. 各ファイルの役割（重要）

- **`assets/main.css`** … テスト環境の本番コンパイル済みCSSをそのまま同梱。**HTMLを単体表示するための参照用**。
  テーマ側に同名ファイルが既にあるため、**これでテーマを上書きしないこと**。
- **`assets/add-serviceDetail.css`** … 今回の改修で**新たに追加したCSS**。これが実装対象。
  中身は `src/scss/page/_serviceDetail.scss` への追記を想定（rem値・`var(--color-*)`はテーマ準拠）。
  `@media screen and (max-width: 960px)` はテーマの `@include mq(md)` に置換可。
- **HTML内 `<head>` の `<script>`（--vw/--vh 補完）** … **静的プレビュー専用**。
  テーマのJS（main.js）が同等処理をするため、WordPress実装時は不要。
- **リンク `href="#"`** … プレビュー用に本番URLを無効化済み。実装時は適切なURL/関数に戻す。
- **`<meta name="robots" content="noindex">`** … プレビュー用。実装時は不要。

## 5. 実装ガイド

### 5-1. service.html（オンライン診療）
既存ページ（特徴／こんな方におすすめ／選ぶ理由／多彩なサービス／サービスの流れ／一覧群）は**そのまま維持**。
変更点は以下の2つのみ。

1. **オンライン診療一覧カードの枠線色をTOPと統一**
   - `pg_home_menu_itemLink` に `data-color="blue|pink|green"` を付与（TOPと同じ分類）。
   - blue: メンズヘルス/男性AGA/ED　pink: 女性更年期/美容/ダイエット/女性AGA/ピル/まつ毛　green: エクソソーム/トラベル/サプリ/不眠/性感染症/花粉症/二日酔い
   - 色は既存CSS（`main.css` の `.pg_home_menu_itemLink[data-color=...]`）で定義済み。**`data-color`属性を出力するだけ**。
2. **「よくある質問」セクションを追加**（`<!-- section-faq -->`）
   - 既存の `el_cardFaq`（FAQアコーディオン）を流用。
   - `single-hometesting.php` の `section-faq` 分岐と同じ構造。ACF flexible content に `section-faq` を追加すれば既存テンプレで出力可能。
   - セクション下の余白用に `.pg_serviceDetail_faq` に `__mbWide` クラスを付与（CSSは add-serviceDetail.css）。

### 5-2. menshealth.html（メンズヘルス診療）
元の `single-onlinecare.php` は3ブロック（target/nayami/kusuri）のみ。以下を追加済み。

| 追加セクション | 実装方法 |
|---|---|
| `section-about`（疾患説明） | `single-hometesting.php` の `section-products`（`el_cardProduct` アコーディオン）を流用 |
| `section-hikaku`（治療薬の比較表） | 新規。`add-serviceDetail.css` の `.pg_serviceDetail_hikaku` |
| `section-price`（料金プラン） | `single-hometesting.php` の `section-price`（`pg_serviceDetail_plan`）を流用 |
| `section-flow`（診療の流れ） | TOPの `components/elements/featureFlow.php`（`el_featureFlow`）を流用 |
| `section-faq`（よくある質問） | `el_cardFaq` を流用 |
| `section-legal`（未承認医薬品の法定記載） | 新規。`add-serviceDetail.css` の `.pg_serviceDetail_legal`。**海外薬掲載時は医療広告ガイドライン上必須** |

**実装の近道**: `single-onlinecare.php` に `single-hometesting.php` の
`section-products` / `section-price` / `section-flow` / `section-faq` のレイアウト分岐をコピーし、
ACF flexible content にも同レイアウトを追加。新規2種（hikaku / legal）は本HTMLのマークアップをテンプレ化。

### 5-3. add-serviceDetail.css の内訳
- `.pg_serviceDetail_flow*` … `_serviceDetail.scss` でコメントアウトされていた flow デザインの復活版
- `.el_featureFlow` … メンズヘルスで流用した flow の上マージン調整
- `.pg_serviceDetail_products_inner` … about のカラム幅を nayami と統一（121.2rem）
- `.pg_serviceDetail_hikaku*` … 比較表
- `.pg_serviceDetail_legal*` … 法定記載ボックス
- `.pg_serviceDetail_faq.__mbWide` … FAQ下の余白

## 6. 【要確認】事項（クライアント／march 仕様の確定待ち）

HTML内に `【要確認】` で明示（menshealth.html に2箇所）。実装前に確定が必要：

- 価格（料金プラン・診察料・配送料）／税込・税別の表記方針
- お薬の配送リードタイム（最短◯日）
- 決済手段（march側の対応：クレジット／コンビニ払い等）
- 海外製薬・早漏薬（ダポキセチン）の取扱有無 → 法定記載の文言調整
- CTA「今すぐ予約」のリンク先（march予約URL）
- フッターの運営医療機関 正式表記（現状プレースホルダ）

## 7. 画像について

- `assets/images/` … テーマ素材（`clinic_2025/assets/images` 相当）
- `assets/wp-content/uploads/` … 本番uploadsのカードサムネイル等
- メンズヘルスのヒーロー `hero-mens.png`、薬剤画像はプレースホルダ（グレー背景）。本実装では適切な画像に差し替え。

---

不明点は本資料の該当箇所と合わせてご連絡ください。

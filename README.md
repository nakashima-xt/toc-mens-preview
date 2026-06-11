# メンズヘルス診療ページ 改修案HTML（現行TOCデザイン版）

作成日: 2026-06-11
対象: /onlinecare/ メンズヘルス診療（男性更年期・EDなど）＝ 投稿ID 170

## ファイル構成

```
html_現行デザイン版/
├── menshealth.html          ← 改修案本体（ブラウザでそのまま開けます）
├── README.md                ← このファイル
└── assets/
    ├── main.css             ← テスト環境の本番コンパイル済みCSS（clinic_2025）そのまま
    ├── add-serviceDetail.css ← 追加セクション用の追補CSS（テーマトーン準拠）
    └── images/              ← テーマリポジトリ（omotesando-clinic-main）の画像
```

- デザインは**現行テーマ clinic_2025 のCSSをそのまま使用**（DMM寄せ版とは別物）。
- ヘッダー/フッターはテスト環境の実HTMLを流用。ヒーロー背景画像のみテスト環境のuploads参照のため、Basic認証(rooph-test/test1234)を一度通すと表示されます。

## ページ構成と実装マッピング

| # | セクション | 使用クラス | 実装方法 |
|---|---|---|---|
| 1 | ヒーロー | `pg_serviceDetail_serviceHeader` | 既存。タイトル・リード文・CTAをACFで更新 |
| 2 | あなたが今気になっていることは？ | `pg_serviceDetail_target` | **既存レイアウト（section-target）**。ACFのリスト4件を差し替え |
| 3 | こんな方におすすめ | `pg_serviceDetail_nayami` | **既存レイアウト（section-recommend）**。リスト4件差し替え |
| 4 | ED・早漏・男性更年期について | `pg_serviceDetail_products` + `el_cardProduct` | **hometestingテンプレの section-products を onlinecare 側に移植**（アコーディオン3件） |
| 5 | お薬などについて | `pg_serviceDetail_kusuri` | 既存（section-kusuri）。薬剤7種＋**説明文を追加**（要 itemDesc 対応） |
| 6 | 自分に合うお薬を選べます（比較表） | `pg_serviceDetail_hikaku`（新規） | 追補CSS参照。新規ブロック |
| 7 | 価格・プラン | `pg_serviceDetail_plan` | **hometestingテンプレの section-price を移植**（価格は【要確認】のまま） |
| 8 | オンライン診療の流れ | `pg_serviceDetail_flow` | hometestingに markup あり。**スタイルは _serviceDetail.scss でコメントアウト中** → 追補CSSで復活させたものを使用 |
| 9 | よくある質問 | `pg_serviceDetail_faq` + `el_cardFaq` | **hometestingテンプレの section-faq を移植**（6問） |
| 10 | お薬に関する重要事項（法定記載） | `pg_serviceDetail_legal`（新規） | 追補CSS参照。**医療広告ガイドライン上、未承認薬掲載時は必須** |
| 11 | 事業者CTA | `pg_home_cta` | 既存のまま |

## WordPress実装時の手順（実装担当向け）

1. `single-onlinecare.php` に、`single-hometesting.php` から以下のレイアウト分岐をコピー:
   `section-products` / `section-price` / `section-flow` / `section-faq`
   （ACF flexible content「contents」にも同レイアウトを追加）
2. 新規2ブロック（hikaku / legal）は本HTMLのマークアップをそのままテンプレ化
3. `assets/add-serviceDetail.css` の中身を `src/scss/page/_serviceDetail.scss` に移植してビルド
   - flow部分は同ファイル内のコメントアウト済みコードの復活＋微修正
4. kusuri カードへの説明文追加: ACFのkusuriリストに `desc` サブフィールドを追加し、
   `<p class="pg_serviceDetail_kusuri_itemDesc">` を出力

## 未確定事項（クライアント/march確認待ち）

- 価格（プラン・診察料・配送料）→ プランカード・FAQ・フロー内の【要確認】
- 配送リードタイム（最短◯日）
- 海外製薬・早漏薬（ダポキセチン）の取扱有無 → 法定記載の文言調整
- CTA「今すぐ予約」のリンク先（march予約URL発行待ち）
- 薬剤画像（現在はグレーのプレースホルダ）

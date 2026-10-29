# movie-browser-site

macOS アプリ **SceneStrip** の App Store 用マーケティングページ（LP）。
アプリ本体は別リポジトリ `movie-browser` で管理し、LP の文言はその `README.md` / `docs/current_spec.md` の範囲を超えないこと。

## 公開先（デプロイ）

| 項目 | 値 |
| --- | --- |
| ホスティング | Cloudflare Pages（S3 等ではない） |
| 公開 URL | https://scene-strip.pages.dev/ |
| デプロイ方法 | GitHub の `main` へ push すると自動デプロイ（ビルドコマンドなし、`Build output directory: public`） |
| 反映時間 | push 後おおむね 1〜2 分 |
| 独自ドメイン | 未設定（取得したら Pages にカスタムドメインを割り当て、`public/_redirects` で `pages.dev` → 独自ドメインへ 301） |

### Cloudflare Pages の挙動で注意する点

- `/foo.html` は `/foo` へ 308 リダイレクトされる（拡張子なし URL が正）。
- `public/404.html` があるので存在しない URL は 404 を返す（無いと `index.html` が 200 で返り、ソフト 404 になる）。
- `public/_headers` でセキュリティヘッダと `assets/*` のキャッシュを設定している。

## フォルダ構成

```
public/
  index.html                       # 日本語トップ（canonical: /）
  en/index.html                    # 英語（/en/）
  zh/index.html                    # 簡体字中国語（/zh/）
  support/index.html               # サポート窓口（App Store の Support URL の飛び先、noindex）
  404.html
  robots.txt / sitemap.xml         # ページを追加したら sitemap.xml に追記する
  _headers / _redirects(未作成)
  googleedb568dd92322edb.html      # Google Search Console の検証ファイル（削除しない）
  assets/
    site.css                       # 全ページ共通 CSS（各 HTML から参照）
    og.png                         # OGP 画像 1200x630（ja.png から生成）
    ja.png                         # ヒーロー画像（アプリのスクリーンショット）
    scenestrip-app-icon.png
    alternativeto-badge.svg          # AlternativeTo のバッジ（https://alternativeto.net/badges/ の Blue / Compact を自前ホスト。フッターからリンク）
```

- 3 言語のページは同じ構造で、`<head>` に canonical / hreflang（ja, en, zh-Hans, x-default=en）/ OGP / JSON-LD（SoftwareApplication）を持つ。
- 文言を変えるときは 3 ページとも揃えること（自動生成の仕組みはなく、手で編集する）。
- プライバシーポリシーは Google ドキュメント（ja / en 別 URL）へリンクしている。LP 内には置いていない。

## 外部サービスとの連携

### App Store

- App Store Connect の Apple ID: `6759131614`
- LP 内の App Store リンクはキャンペーン計測付き（App Store Connect → アナリティクス → 獲得 → キャンペーン で確認できる）:
  - `https://apps.apple.com/jp/app/apple-store/id6759131614?pt=271041&ct=lp-ja&mt=8`（ja）
  - `https://apps.apple.com/app/apple-store/id6759131614?pt=271041&ct=lp-en&mt=8`（en）
  - `https://apps.apple.com/app/apple-store/id6759131614?pt=271041&ct=lp-zh&mt=8`（zh）
  - `pt=271041` は Provider ID。`ct` は流入元ごとに変える（記事なら `blog-<slug>` など）。
- App Store Connect 側の Marketing URL: ja → `/`、en → `/en/`、zh-Hans → `/zh/`。Support URL は全ロケール `/support/`。

### Google Search Console

- プロパティ: `https://scene-strip.pages.dev/`（URL プレフィックス型）
- 所有権確認: HTML タグ方式。各ページ `<head>` の
  `<meta name="google-site-verification" content="QAWB_DFqbJ67yoJN5sh-e6iWXcxPh6-TODOH9izL2sg" />`
  と `public/googleedb568dd92322edb.html` が検証用。**どちらも削除しない**（削除すると所有権が外れる）。
- サイトマップ `https://scene-strip.pages.dev/sitemap.xml` を送信済み（2026-10-28）。
- 新ページを追加したら URL 検査から「インデックス登録をリクエスト」する（新規プロパティは 1 日あたりの回数制限が小さい）。

### お問い合わせフォーム

- ja: https://forms.gle/4u6ZD7acUpVw8z4b6
- en: https://forms.gle/RErxR7UYiTxx3KSh8

## ローカル確認

```bash
cd public && python3 -m http.server 8787 --bind 127.0.0.1
# http://127.0.0.1:8787/ , /en/ , /zh/ , /support/
```

## 関連ドキュメント

- ASO 文言案・LP 改善案・反映状況: `movie-browser/docs/releases/aso_proposal_2026-10.md`

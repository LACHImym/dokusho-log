# 色名MASTER 引き継ぎメモ（HANDOFF）

別のAIモデル／セッションが続きを担当できるようにまとめた記録。
Notion「DB指示履歴」にも同内容のページあり（概要:「色名MASTER…引き継ぎまとめ」）。

## 基本情報
- **本番URL**: https://lachiart.com/quiz/iro/ （ロリポップに手動アップロードで公開。名画MASTERと同方式）
- **ソース**: GitHub `LACHImym/dokusho-log` / ブランチ `claude/color-name-quiz-app-pa3fdo` / `quiz/iro/` 配下
- **ポータル**: https://lachiart.com/quiz/ （名画・色名を並べるクイズ広場。ソースは `LACHImym/meiga-master` の `upload-to-lolipop/quiz/index.html`）
- **姉妹アプリ 名画MASTER**: https://lachiart.com/quiz/meiga/ / repo `LACHImym/meiga-master`（実装リファレンス。`app.js`）

## 構成ファイル（quiz/iro/）
| ファイル | 内容 |
|---|---|
| `index.html` | 本体。単体で動作（CSS/JSインライン）。ビルド不要 |
| `colors.js` | 出題データ。`window.IRO_LEVELS` と `window.IRO_COLORS` |
| `logo.png` / `favicon.png` / `og-card.png` | **らち提供の公式画像**（自作しない） |
| `README.md` | 詳細仕様 |

## データ（colors.js）※重要方針
- Notion「色彩図鑑」DB（`collection://3a1ffcba-f9f0-80aa-8401-000b11b002fa`、i-iro.com由来・**計987色**）を書き出したもの。
- 各色: `n`=名前 / `y`=よみ・綴り / `h`=webcolor / `f`=系統色(選択肢生成用) / `d`=由来概要 / `lv`=レベル。
- `lv1`=和の色267 / `lv2`=世界の色720（よみの文字種で自動判定）。
- 更新の合図は「**クイズのデータを更新して**」（名画と同じ運用）。
- ⚠️ **らち指示**: `colors.js` は他所（i-iro.com）データ。**データ自体を見せる機能（図鑑コンプ／今日の色コラム等）は実装しない**。クイズの出題に使うのはOK。
- ⚠️ **らち指示**: ロゴ・キュビコン・OGP等の画像は**自作SVGにせず、らち提供の公式PNGをそのまま使う**（同名で差し替え）。

## デザイン（らち提供モックに準拠）
- フラット白基調。背景白、選択肢はA/B/C/Dの円形バッジ＋色名。
- 回答後：各バッジを実際の色で塗る（文字色は明度で白/紺自動）、正解=マゼンタ枠＋マゼンタ文字、選んだ誤答=取り消し線、他の誤答=グレー。
- アクセントは**名画masterと同じティール**（`--teal:#2dd4bf` / `--teal-deep:#0d9488`）。※以前オレンジで作ったが、らち指示で名画と同じ青緑に統一済。
- マゼンタ `--pink:#e4007f`（正解ハイライト）。
- 結果は円形スコアダイヤル（SVGアーク／名画master準拠）。
- ロゴ「色タMASTER」＋SHIKIMEI＋キュビコン（公式PNG）。未設置時はテキストにフォールバック。

## デイリー機能（第1弾・名画masterレシピ準拠／Wordle方式・ログイン不要）
- **今日の5色**: 日付シード `mulberry32(hashStr('iro-'+YYYY-MM-DD))` で全員同一・翌0時に自動更新・サーバー不要。構成は段階式（和×2→世界×2→全色×1）。
- **#N**: `DAILY_EPOCH="2026-07-24"` を #1 として日数で算出。⚠️ 実launch日を#1にしたい場合は1行調整。
- **ストリーク/成績**: `localStorage` キー `"iroMaster_state"`（lachiart.comドメイン共有のため接頭辞必須）。前回が昨日なら+1、途切れたら1、一昨日以前は表示0。**デイリーは1日1回のみ記録**。
- **画面4状態**: ホーム/出題/結果/きろく。出題中は「← やめる」で中断可。
- **きろく**: 🔥連続/最高連続/プレイ回数/平均点＋直近にあそんだ色10件＋プレイ履歴。
- **結果**: デイリー完走で「🔥n日連続！明日0時に #N+1」予告。

## 広告（AdSense）
- `client=ca-pub-6177307831535485` / `slot=2286251553`（**名画masterと同一**）。
- ヘッダー直下に常時表示（クローラーはクイズを遊ばない→hidden画面内の広告は認識されない知見）。
- 埋まらない場合は枠を自動で畳む。次の一手＝色の豆知識など読み物を足して文字量増。

## シェア / OGP
- `og-card.png`（1201×631）。`og:image`/`twitter:image` に `?v=2`、シェアURLに `?v=3`（SNS旧キャッシュ回避）。`twitter:card=summary_large_image`＋width/height/alt/title/description 明示済。
- ハッシュタグ: `色名MASTER,色の名前,色彩検定`。
- 反映しない時は X Card Validator / FB シェアデバッガーで再スクレイプ。
- **名画master側**: `app.js` のシェア文面（デイリー/レベル両方）末尾に `#美術検定` を追加した差し替え用 app.js を提供済（`DAILY_EPOCH=2026-07-19`）。

## デプロイ（ロリポップ 手動）
- `quiz/` 直下で `iro.zip`（iro/フォルダ入り）を解凍 → `quiz/iro/` になる。
- ⚠️ **更新時は先に対象フォルダを削除**してから解凍（zip解凍は既存を上書きせずスキップ）。単体ファイルはアップロード上書きでOK。

## 未了・申し送りタスク
- [ ] 最新 `iro.zip`（OGP補強＋#色彩検定版）をロリポップにアップして本番反映・表示確認
- [ ] ポータル `quiz/index.html` を色名カード追加版に差し替え（提供済・要アップロード）
- [ ] 名画 `quiz/meiga/app.js` を #美術検定版に差し替え（提供済・要アップロード）
- [ ] 公開後 OGP を Card Validator 等で再スクレイプ
- [ ] `DAILY_EPOCH` の #1 日付を実launch日に合わせるか確認
- [ ] （構想）第2弾: ニックネーム＋Supabase で今日/週間ランキング・端末間同期
- [ ] （構想）第3弾: Xログイン・バッジ/称号・毎朝X自動投稿・ポータル相互送客

## 作業環境メモ（次のAIへ）
- 作業ブランチ `claude/color-name-quiz-app-pa3fdo`、`quiz/iro/` が色名MASTER。
- プレビューは claude.ai の Artifact（colors.js と logo を埋め込んだ単体HTMLを生成して公開する運用）。
- ブラウザ検証は Playwright（`/opt/pw-browsers/chromium`、グローバル `npm root -g` の playwright を利用）。
- チャットに貼られた画像はファイルとして取り込めない → 公式画像はリポジトリ/ロリポップ経由でもらう。

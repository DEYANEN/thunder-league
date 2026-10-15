# 雷 LEAGUE — GitHub Pages + Firebase

スマホ・PC対応。順位表が最初に表示され、対戦表から各カードの2試合分を確認できます。通常スコアとPKスコアは別入力。順位計算はブラウザ側で行い、データはFirebase Firestoreで全端末に同期します。

## 公開までの手順
1. [Firebase Console](https://console.firebase.google.com/) でプロジェクト作成。Webアプリを追加し、表示される `firebaseConfig` の4項目を `config.js` に貼り付ける。
2. Authentication → Sign-in method で **Google** を有効化。Settings → Authorized domains に GitHub Pages のドメイン（`ユーザー名.github.io`）を追加する。
3. Firestore Database を作成する。公開ルールはまだ設定せず、最初に `firestore.rules` の `REPLACE_WITH_YOUR_GOOGLE_UID` を自分のGoogleログインUIDに置き換える。UIDの調べ方：仮公開したサイトでGoogleログインし、管理者タブに表示されるUIDをコピーする。初回ログインのため、まず `config.js` の `adminUid` は仮値でもよい。
4. `config.js` の `adminUid` も同じUIDに変更し、Firestore → Rules に `firestore.rules` の内容を貼り付けて **公開** する。
5. サイトの管理者タブからGoogleログインして **初期データを登録** を1回押す（既存データがある場合は押さない）。これで全端末に同じデータが表示される。
6. このフォルダのファイルをGitHubの公開リポジトリのルートへアップロード。Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` を選択して保存。数分後に `https://ユーザー名.github.io/リポジトリ名/` で公開。

## 運用
- 閲覧：誰でもログイン不要。
- 編集：`config.js` の `adminUid` と Firestore Rules に設定したUIDでログインした本人のみ。**編集制限の本体はFirestore Rulesです。** GitHubリポジトリの公開状態は編集権限に影響しません。
- `config.js` のFirebase APIキーはWeb用の公開設定値であり、秘密鍵ではありません。アクセス制御はFirestore Rulesで行います。管理者UIDの一致だけでなくGoogleアカウント自体も保護してください。
- 対戦表の各カードを押し、1試合目・2試合目の通常スコアと（引分の場合）PKスコアを入力します。`3-1` のように左選手-右選手の順です。
- 未実施の試合は空欄。引分のスコアを保存する際はPK結果必須。PKスコアは得点・失点・得失点差に含めません。
- 順位：勝点→得失点差→総得点→同率グループ内の当該勝点→当該得失点差→当該得点→登録順。2試合を合算。3人以上の同率もグループ内の直接対戦を合算。途中段階で同率グループを再分割しての再計算はしません。
- 初期データ登録ボタンは既存結果を上書きします。初期登録時にだけ押してください。

## ローカルプレビュー
ES Modulesを使用するため、ファイルを直接ダブルクリックせず、フォルダ内で `python -m http.server 8000` を実行し `http://localhost:8000` を開いてください。Firebase認証にはlocalhostがAuthorized domainsに必要です。

## ファイル
`index.html` 画面 / `style.css` デザイン / `app.js` 計算・同期 / `data.json` 選手初期値 / `config.js` Firebase接続情報 / `firestore.rules` アクセス制御。

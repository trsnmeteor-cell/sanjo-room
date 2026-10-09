# 三条河原町 部屋一覧

カラオケボックスの部屋台帳をブラウザだけで扱う、単一ファイルの業務アプリ（試作）です。
`index.html` 1枚で動き、ビルドも外部依存もありません。

## できること

- **トップに「三条河原町　部屋一覧」**、中央に部屋番号の升目（横長9列）
- 左のタブで2モードを切替
  - **通常モード（一覧・確認）** — 部屋を押すと編集画面が開く
  - **作業モード（点検・完了）** — 編集は不可。部屋を押すと作業完了の印が付き、もう一度押すと取り消し
- 升を押すと**編集ポップアップ**が開く（通常モード）
  - 登録した写真は**プレビューをクリック（または「拡大表示」）で全画面に拡大**できます（✕・画面クリック・Escで閉じる）
  - カラオケ機種・選択方式（LAI / AIR / LDW / JM / JM2 / JMG / JX1）
  - 入り口から見た部屋全体の写真（1枚・自動縮小して保存）
  - モニターの型番（文字入力）
  - **エアコンの型番（文字入力／無い部屋は「なし」）**
  - **部屋にカメラがあるか（有・無）**
  - 清掃実施日（日付入力・YYYY/MM/DD）
  - 清掃実施者（氏名入力）
  - **最終更新日時は登録時に自動反映**
- **7項目すべて入力しないと登録できない**（フッターに残りの必須項目を表示）
- 通常モードでは登録済みの部屋が階の色で点灯。**清掃実施日から2週間（14日）以上経過した部屋はピンク（要清掃）**で表示されます
- モードごとの色の優先順位
  - **通常モード**：異常事態（赤）＞ 清掃から2週間経過（ピンク）＞ 登録済み（階の色）／未登録（灰）
  - **作業モード**：異常事態（赤）＞ 作業完了（緑）／未完了（灰）※清掃経過の色は出ません
  - **異常事態モード**：異常事態（赤）のみ。それ以外はすべて灰（他の状態に影響されません）
- 左下の凡例は**モードごとに切り替わります**
- 作業モードでは**未完了＝灰色／完了＝緑**。押すたびに切替わり（誤操作は2回押しで戻せる）、
  左レールに「作業完了 n / 61」が出ます。**「作業完了をすべて取り消す」**で一括解除できます
- 左レールの「バックアップを書き出す／読み込む」で JSON の保存・復元

## 部屋構成

| 段 | 部屋 |
|---|---|
| 1段目 | 101〜108 |
| 2段目 | 201〜209 |
| 3段目 | 301〜309 |
| 4段目 | 310〜313 |
| 5段目 | 401〜409 |
| 6段目 | 410〜416 |
| 7段目 | 501〜509 |
| 8段目 | 510〜515 |

## GitHub Pages で公開する

## デプロイすると自動で最新版に更新されます

このアプリは**開いたままでも自動で最新版へ切り替わります**。仕組みは次のとおりです。

- リポジトリに `version.json`（例：`{"version":"30d6ce4481a4"}`）を置いています
- GitHub Actions が**デプロイのたびに、そのときのコミットSHAを `version.json` と `index.html` の両方に刻みます**（ワークフローの「Stamp build version」ステップ）
- アプリは 起動時・タブに戻ったとき・約30秒ごとに `version.json` を（キャッシュを使わず）確認し、
  **自分のバージョンと違えば自動で再読み込み**します。再読み込みは `?v=<バージョン>` を付けて行うため、
  CDNやブラウザに残った古いHTMLを使わずに新しい版を取得できます

つまり **Actions でのデプロイ（方法A）なら、`git push` するだけで利用者の操作なしに最新版へ切り替わります**。

### 編集画面を開いているときは

入力中に勝手に消えると困るため、**何かのポップアップを開いている間は更新を保留し**、
「新しいバージョンがあります。画面を閉じると自動で更新します」と表示します。画面を閉じた時点で自動で切り替わります。

### 方法B（画面からファイルを直接アップロード）で公開する場合

Actions を通らないためバージョンが自動で刻まれません。この場合は、更新のたびに
`version.json` の `version` の値（例：`{"version":"1"}` → `{"version":"2"}`）を書き換えてアップロードしてください。
それだけで各端末が新しい版を検知して自動更新します（`index.html` 側は変更不要です）。

### 方法A：コマンドで（推奨）

```bash
git init -b main
git add .
git commit -m "三条河原町 部屋一覧アプリ"
gh repo create sanjo-kawaramachi-rooms --public --source=. --push
```

公開後、リポジトリの **Settings → Pages → Source** を **「GitHub Actions」** に設定してください。
`.github/workflows/pages.yml` が自動でデプロイします（`main` に push するたび更新）。

公開URL：`https://<ユーザー名>.github.io/sanjo-kawaramachi-rooms/`

### 方法B：画面から

1. GitHub で新しいリポジトリを作成（Public）
2. 「uploading an existing file」で `index.html` / `README.md` / `.nojekyll` をドラッグして Commit
3. **Settings → Pages → Source** を「Deploy from a branch」、Branch を `main` / `/(root)` に設定
4. 1〜2分で公開URLが発行される

## 構成

```
index.html                     アプリ本体（HTML / CSS / JS をすべて内包）
.github/workflows/pages.yml     GitHub Pages への自動デプロイ
.nojekyll                       Jekyll 処理の無効化
```

## データの保存について（重要）

- 保存先は**閲覧しているブラウザの localStorage**（未設定時）または**Googleスプレッドシート**（同期設定時）です。別の端末・別のブラウザ・シークレットウィンドウでは共有されません。
- 保存する内容は2つに分かれています。
  - `sk3-room-registry-v1` … 部屋の登録内容（機種・写真・型番・清掃記録）
  - `sk3-room-registry-v1-work` … 作業モードの完了印（作業セッションごとにリセット可）
- 写真は長辺560px・JPEG品質0.6に自動縮小して保存します（1枚あたり約30〜60KB）。
  上限に達した場合はその旨が画面に表示されます。
- 複数端末で共有する場合は下の「複数端末での共有（Googleスプレッドシート）」を設定してください。
- 定期的に「バックアップを書き出す」でJSONを保存しておくと安全です。


## 作業担当者（清掃実施者）のリスト

編集画面の「清掃実施者（作業担当者）」は**プルダウン**です。

- リストにある名前を選ぶだけ
- リストに無い人は「**＋ 新しい名前を登録…**」を選び、名前を入力して「登録」→ 以後は全端末のプルダウンに並びます
- 「選択中の名前をリストから削除」でリストから外せます（過去の記録は残ります）

## 画面の構成（PC）

- 左レール：アプリ名／**通常モード・作業モード・異常事態モード**のタブ（`通常` と `モード` の2行表示でサイズ固定）／その隣に **⚙ 設定** ボタン／下に登録件数
- **モードボタンの大きさは切り替えても変わりません**（どのモードを選んでも同じサイズ）
- **⚙ 設定**を押すとポップアップが開き、そこで「今すぐ同期／スプレッドシート同期の設定／バックアップを書き出す／バックアップを読み込む」を行います
  - 作業モードのときは、このポップアップに「作業完了をリセット」も表示されます
  - **部屋一覧のページには同期設定を出しません**（設定はポップアップ内に集約。歯車の右上の点で同期の状態だけが分かります：緑＝同期オン／赤＝エラー／灰＝同期オフ）

### モバイル表示

- 3モードのタブは画面上部に**横並び**（`通常`／`モード` の2行表示）、その右端に **⚙ 設定** ボタン
- タブの高さは揃ったまま、モードを切り替えても**サイズが変わりません**
- 同期設定・バックアップは **⚙ 設定** のポップアップ内（一覧には表示しません）
- 9列の升目は**1画面に収まります**（升は番号＋小さな状態記号のみのコンパクト表示。横スクロールはしません）
  - 状態記号：正常な登録済＝`●`（階の色）／作業完了＝`✓`（緑）／異常事態＝`⚠`（赤）
- 編集・異常事態・同期設定の各ポップアップは**画面幅に合わせて1列**で表示されます

## 異常事態モード（3つ目のモード）

左のタブは **通常モード／作業モード／異常事態モード** の3つです。

- 異常事態モードで部屋を押すと、**一言の入力ポップアップ**が開きます
- 内容を入力して「登録する」と、その部屋は**異常事態＝使用不可**になります
- 異常事態の部屋は**赤（斜線ハザード）で点灯**し、**通常モードにも作業モードにも反映**されます
  - 通常モード：`⚠ 異常` と内容を表示
  - 作業モード：`⚠ 使用不可` と表示し、**作業完了にはできません**（押すと理由を表示）
  - 異常事態モード：`⚠ 発生中` と内容を表示
- 同じ部屋をもう一度開くと内容の**更新**、または「**解除して使用可に戻す**」で正常に戻せます
- 左レールの件数はモードに応じて「登録済／作業完了／異常事態」に切り替わります
- 異常事態の情報もスプレッドシート同期の対象です（キーは `incident:<部屋番号>`）

## 複数端末での共有（Googleスプレッドシート）

静的ホスティング（GitHub Pages）だけでは端末同士は共有できません。そこで **Googleスプレッドシート**を
台帳の共有先にします。サーバー代もAPIキーも不要で、Googleアカウントだけで完結します。

### しくみ

`ブラウザ（アプリ）` ⇄ `Apps Script のウェブアプリ（JSON API）` ⇄ `Googleスプレッドシート`

- 読み取りは **JSONP**（`<script>` 経由）で行うため、ブラウザの CORS 制限を受けません
- 書き込みは **`no-cors` の POST**（`Content-Type: text/plain`）で行います。応答は読めないため
  「投げっぱなし」になりますが、次回の同期でシート側の更新日時と突き合わせ、
  **まだ反映されていなければ自動で再送**されます
- 競合した場合は**更新日時が新しい方**が残ります（`rev`＝ミリ秒タイムスタンプで比較）

### 1. スプレッドシートとApps Scriptを用意する（初回のみ）

1. Googleドライブで**スプレッドシートを新規作成**（名前は任意）
2. メニューの **拡張機能 → Apps Script** を開く
3. 下記コードを貼り付けて**保存**（`room_state` シートと見出し行は自動作成されます）
4. **デプロイ → 新しいデプロイ → 種類「ウェブアプリ」**
5. **実行ユーザー＝自分／アクセスできるユーザー＝全員** にして「デプロイ」
6. 表示された**ウェブアプリのURL**（`.../exec`）を控える

```javascript
const SHEET_NAME = 'room_state';

function doGet(e) {
  const p = (e && e.parameter) || {};
  const cb = p.cb || '';
  let payload;
  try {
    const sh = getSheet_();
    const values = sh.getDataRange().getValues();
    const since = p.since ? new Date(p.since).getTime() : 0;
    const rows = [];
    for (let i = 1; i < values.length; i++) {
      const key = values[i][0];
      if (!key) continue;
      const raw = values[i][1];
      const data = raw ? JSON.parse(String(raw)) : {};
      const updated = (values[i][2] instanceof Date) ? values[i][2].toISOString() : String(values[i][2] || '');
      if (since && updated && new Date(updated).getTime() <= since) continue;
      rows.push({ key: key, data: data, updated_at: updated });
    }
    payload = { ok: true, rows: rows };
  } catch (err) {
    payload = { ok: false, error: String(err) };
  }
  const body = cb ? (cb + '(' + JSON.stringify(payload) + ')') : JSON.stringify(payload);
  return ContentService.createTextOutput(body)
    .setMimeType(cb ? ContentService.MimeType.JAVASCRIPT : ContentService.MimeType.JSON);
}

function doPost(e) {
  let result = { ok: true, count: 0 };
  try {
    const body = JSON.parse((e && e.postData && e.postData.contents) || '{}');
    if (body.action !== 'upsert') throw new Error('unknown action');
    const rows = body.rows || [];
    const sh = getSheet_();
    const last = sh.getLastRow();
    const values = last > 1 ? sh.getRange(2, 1, last - 1, 3).getValues() : [];
    const at = {};
    for (let i = 0; i < values.length; i++) at[values[i][0]] = i + 2;
    const now = new Date();
    rows.forEach(function (r) {
      const row = [r.key, JSON.stringify(r.data || {}), new Date(r.updated_at || now)];
      if (at[r.key]) { sh.getRange(at[r.key], 1, 1, 3).setValues([row]); }
      else { sh.appendRow(row); at[r.key] = sh.getLastRow(); }
    });
    result.count = rows.length;
  } catch (err) {
    result = { ok: false, error: String(err) };
  }
  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}

function getSheet_() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let sh = ss.getSheetByName(SHEET_NAME);
  if (!sh) sh = ss.insertSheet(SHEET_NAME);
  if (sh.getLastRow() === 0) {
    sh.appendRow(['key', 'data', 'updated_at']);
    sh.setFrozenRows(1);
  }
  return sh;
}
```

> デプロイ設定は必ず「実行ユーザー＝自分（デプロイした本人）」「アクセス＝全員」にしてください。
> 「アクセス＝自分のみ」だと他の端末から読めません（[Apps Script 公式：Web Apps](https://developers.google.com/apps-script/guides/web)）。

### 2. アプリ側で設定

左レールの「**スプレッドシート同期の設定**」を開き、URLを貼って「**接続テスト**」→「**保存して同期**」。
設定は端末のブラウザに保存されるので、2台目以降も同じURLを一度貼るだけです。
ダイアログの「**コピー**」ボタンで上のApps Scriptコードをそのままコピーできます。

### 3. 同期されるタイミング

| タイミング | 動作 |
|---|---|
| アプリを開いた／再読み込みした | シートから最新を取得 |
| 登録・更新した | その部屋だけ即送信 |
| 作業モードで完了を付け外しした | その部屋だけ即送信 |
| 担当者リストを追加・削除した | リストを即送信 |
| タブに戻った・ウィンドウを前面にした | 取得＋未送信分を送信 |
| オンラインに復帰したとき | 取得＋送信 |
| 表示中 約30秒ごと／「今すぐ同期」 | 取得＋送信 |

### ⚠️ セキュリティ上の注意

- ウェブアプリのURLを知っている人は、**誰でも台帳を読み書きできます**。URLは社外に共有しないでください
  （アプリ側の設定も各端末のブラウザ内にだけ保存されます＝リポジトリには入りません）。
- 社内に限定したい場合は、Apps Script 側でGoogleアカウントを確認する処理を足すか、
  アクセス範囲を「全員（Googleアカウントを持つユーザー）」に変更して運用してください。
- シート側には過去の状態が残るため、**誤操作の復旧や履歴確認にも使えます**。

### スプレッドシートを使わない場合

未設定のままならアプリは**その端末のブラウザ内だけ**で動きます（従来どおり）。
左レールの「バックアップを書き出す／読み込む」でJSONの持ち出し・移行もできます。

## 今後の拡張候補

- 機種コードの増減（実際のラインナップに合わせる）
- 写真の複数枚対応・拡大表示
- 階・担当者での絞り込み、清掃期限のアラート
- 作業完了の担当者・日時の記録（誰がいつ点検したか）
- 共有DBへの移行（ログイン・共同編集）

## ライセンス

未設定。必要であれば追加してください。

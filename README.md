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
  - **右上に「要清掃 n件（清掃から14日以上）」を表示**し、凡例にも同じ件数を出します（判定が効いているか一目で確認できます）
  - 清掃実施日の読み取りは `2026-10-01` / `2026/10/1` / `2026年10月1日` / ISO日時 などに対応し、保存時に `YYYY-MM-DD` へ自動で正規化します
  - 読み取れない形式の清掃日がある部屋は「清掃日の形式が読めない部屋 n件」として件数を表示します（判定漏れの原因が分かります）
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
const CONFLICT_SHEET = 'conflicts';
const CODE_VERSION = 5;

/* ===============================================================
   「サーバー（スプレッドシート）が正」の方式
     - 版番号(seq)はサーバーだけが発行する（端末の時計は使わない）
     - 書き込みは「自分が知っている版番号(baseSeq)」を添えて送る
     - サーバー側が進んでいれば書き込まず、競合として返す（上書きしない）
   =============================================================== */

function doGet(e) {
  const p = (e && e.parameter) || {};
  const cb = p.cb || '';
  let payload;
  try {
    if (p.action === 'upsert') {
      payload = applyRows_(p.rows || '', p.author || '');
    } else if (p.action === 'ping') {
      payload = { ok: true, pong: true, version: CODE_VERSION, serverTime: new Date().toISOString(), rows: [] };
    } else {
      const sh = getSheet_();
      const values = sh.getDataRange().getValues();
      const since = p.since ? new Date(p.since).getTime() : 0;
      const want = p.keys ? String(p.keys).split(',') : null;
      const light = String(p.light || '') === '1';
      const byKey = {};
      for (let i = 1; i < values.length; i++) {
        const key = values[i][0];
        if (!key) continue;
        if (want && want.indexOf(key) < 0) continue;
        let d = {};
        try { d = values[i][1] ? JSON.parse(String(values[i][1])) : {}; } catch (err) { d = {}; }
        const updated = (values[i][2] instanceof Date) ? values[i][2].toISOString() : String(values[i][2] || '');
        const seq = Number(values[i][3] || 0);
        const author = String(values[i][4] || '');
        if (since && updated && new Date(updated).getTime() <= since) continue;
        const prev = byKey[key];
        if (prev && seq <= prev.seq) continue;      // 重複行は版番号が大きいものを採用
        const row = { key: key, data: d, updated_at: updated, seq: seq, author: author };
        if (light) slim_(row);
        byKey[key] = row;
      }
      payload = {
        ok: true, version: CODE_VERSION, serverTime: new Date().toISOString(),
        rows: Object.keys(byKey).map(function (k) { return byKey[k]; })
      };
    }
  } catch (err) {
    payload = { ok: false, error: String(err) };
  }
  const body = cb ? (cb + '(' + JSON.stringify(payload) + ')') : JSON.stringify(payload);
  return ContentService.createTextOutput(body)
    .setMimeType(cb ? ContentService.MimeType.JAVASCRIPT : ContentService.MimeType.JSON);
}

/* POST（互換用）。通常は GET を使う（302転送で本文が失われる環境があるため） */
function doPost(e) {
  let result;
  try {
    const body = JSON.parse((e && e.postData && e.postData.contents) || '{}');
    if (body.action !== 'upsert') throw new Error('unknown action');
    result = applyRows_(body.rows || [], body.author || '');
  } catch (err) {
    result = { ok: false, error: String(err) };
  }
  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}

/* 写真だけを外した軽い表現に変換する */
function slim_(row) {
  const d = row.data || {};
  const hasPhoto = !!d.photo;
  const photoLen = hasPhoto ? String(d.photo).length : 0;
  const slim = {};
  Object.keys(d).forEach(function (k) { if (k !== 'photo') slim[k] = d[k]; });
  slim.hasPhoto = hasPhoto;
  row.data = slim;
  row.light = true;
  row.photoLen = photoLen;
  return row;
}

function applyRows_(rowsRaw, author) {
  let rows = rowsRaw;
  if (typeof rows === 'string') {
    try { rows = rows ? JSON.parse(rows) : []; } catch (err) { throw new Error('rows を解釈できません'); }
  }
  if (!Array.isArray(rows)) throw new Error('rows が配列ではありません');
  const lock = LockService.getScriptLock();
  let result = { ok: true, applied: [], conflicts: [], count: 0 };
  try {
    lock.waitLock(20000);
    const sh = getSheet_();
    const last = sh.getLastRow();
    const values = last > 1 ? sh.getRange(2, 1, last - 1, 5).getValues() : [];
    const at = {};
    const cur = {};
    for (let i = 0; i < values.length; i++) {
      const k = values[i][0];
      if (!k || at[k]) continue;
      at[k] = i + 2;
      let d = {};
      try { d = values[i][1] ? JSON.parse(String(values[i][1])) : {}; } catch (err) { d = {}; }
      cur[k] = { seq: Number(values[i][3] || 0), data: d };
    }
    let maxSeq = 0;
    Object.keys(cur).forEach(function (k) { if (cur[k].seq > maxSeq) maxSeq = cur[k].seq; });
    const now = new Date();
    const conflicts = [];
    rows.forEach(function (r) {
      if (!r || !r.key) return;
      const serverSeq = cur[r.key] ? cur[r.key].seq : 0;
      const baseSeq = Number(r.baseSeq || 0);
      if (baseSeq < serverSeq) {
        /* ほかの端末が先に更新している＝上書きしない。両方を返して知らせる */
        const srvRow = { key: r.key, data: cur[r.key] ? cur[r.key].data : {}, seq: serverSeq, light: true };
        slim_(srvRow);
        conflicts.push({ key: r.key, serverSeq: serverSeq, baseSeq: baseSeq, server: srvRow.data, client: r.data || {} });
        return;
      }
      const incoming = r.data || {};
      const old = cur[r.key] ? cur[r.key].data : {};
      if (!('photo' in incoming) && old.photo) incoming.photo = old.photo;   // 端末に写真が無いときは残す
      maxSeq += 1;
      const row = [r.key, JSON.stringify(incoming), now, maxSeq, String(author || '')];
      if (at[r.key]) { sh.getRange(at[r.key], 1, 1, 5).setValues([row]); }
      else { sh.appendRow(row); at[r.key] = sh.getLastRow(); }
      cur[r.key] = { seq: maxSeq, data: incoming };
      result.applied.push({ key: r.key, seq: maxSeq });
    });
    if (conflicts.length) logConflicts_(conflicts, now);
    result.conflicts = conflicts;
    result.count = result.applied.length;
    result.serverTime = now.toISOString();
  } catch (err) {
    result = { ok: false, error: String(err) };
  } finally {
    try { lock.releaseLock(); } catch (err) {}
  }
  return result;
}

/* 競合した内容は捨てずに別シートへ記録する（後から確認できる） */
function logConflicts_(conflicts, now) {
  try {
    const ss = SpreadsheetApp.getActiveSpreadsheet();
    let sh = ss.getSheetByName(CONFLICT_SHEET);
    if (!sh) {
      sh = ss.insertSheet(CONFLICT_SHEET);
      sh.appendRow(['at', 'key', 'server_seq', 'client', 'server']);
      sh.setFrozenRows(1);
    }
    conflicts.forEach(function (c) {
      sh.appendRow([now, c.key, c.serverSeq, JSON.stringify(c.client || {}), JSON.stringify(c.server || {})]);
    });
  } catch (err) { /* 記録できなくても本体は続行 */ }
}

function getSheet_() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let sh = ss.getSheetByName(SHEET_NAME);
  if (!sh) sh = ss.insertSheet(SHEET_NAME);
  if (sh.getLastRow() === 0) {
    sh.appendRow(['key', 'data', 'updated_at', 'seq', 'author']);
    sh.setFrozenRows(1);
    return sh;
  }
  /* 旧形式（3列）なら見出しと列を追加する */
  const head = sh.getRange(1, 1, 1, 5).getValues()[0];
  if (String(head[3] || '') !== 'seq') {
    sh.getRange(1, 4).setValue('seq');
    sh.getRange(1, 5).setValue('author');
    const last = sh.getLastRow();
    if (last > 1) {
      const seqs = [];
      for (let i = 2; i <= last; i++) seqs.push([i - 1, '']);
      sh.getRange(2, 4, seqs.length, 2).setValues(seqs);
    }
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

## 同期の信頼性アップ（2026-10 更新）

### 送信キューと再送
- 登録・変更はまず「送信キュー」に入り、サーバーへ送ったあと**本当に反映されたか**をキー指定で読み直して確認します。
- 反映が確認できない場合は自動で最大5回まで再送します。それでも入らない場合は次回の同期で再挑戦します。
- 画面右上（設定ボタン内）に「未送信 n件（確認・再送中）」と出ている間は、まだサーバーに入っていません。完了すると「同期オン（最終 …）」に変わります。

### Apps Script の更新（必須）
以前のバージョンをお使いの場合は、**Apps Script を新しいコードに貼り替えて再デプロイ**してください。
1. スプレッドシート → 拡張機能 → Apps Script を開く。
2. `Code.gs` の中身を、この README の Apps Script コードで丸ごと置き換える。
3. 右上「デプロイ」→「デプロイを管理」→ 鉛筆（編集）→ バージョン「新バージョン」→「デプロイ」。
4. 発行される `/exec` URL が以前と同じなら、各端末の設定変更は不要です。

主な変更点: 同時書き込みをロックで直列化、更新日時をサーバー時刻で記録、同じキーの行が重複しても最新（rev 最大）を採用、`keys=` 指定取得に対応。

### 保存容量が足りないとき
- 写真が多いと端末の保存容量（約5MB）を超えることがあります。
- 設定内の「写真を縮小して容量を空ける」で、登録済み写真をまとめて縮小できます。
- それでも保存できない場合は、登録時に「保存容量の上限に達しました」と表示されます。


## 写真が重くても同期が止まらない仕組み（2026-10 更新）

- **同期は写真を運びません。** 取り込み・送信するのは台帳の中身（機種・型番・カメラ・清掃日・担当者）だけで、数十KBです。写真が数百枚あっても同期は軽いままです。
- **写真はサーバー（スプレッドシート）に保存**され、端末では「その部屋を開いたとき」に個別に取得します。端末には直近に見た分だけが残ります。
- 端末の保存容量（目安5MB）がいっぱいになった場合は、**古い写真から端末内の分だけを外し、台帳の情報は必ず保存**します（写真自体はサーバーに残るので、開けばまた表示されます）。設定画面に「端末の保存使用量」が出ます。
- 端末の写真が無い状態で登録し直すと、サーバー側の写真が消える恐れがあるため保存を止めます。「写真を読み込む」で取得してから登録してください。

### 接続できないとき（「応答がありません（タイムアウト）」）
1. Apps Script の「デプロイを管理」で、**アクセスできるユーザー＝全員**になっているか（自分のみだと他の端末から読み込めません）
2. URLの末尾が **/exec** か（/dev は他の端末から使えません）
3. 保存したあと、**必ず「新バージョン」でデプロイ**したか（古いコードのままだと重複や時刻ずれが残ります）

「接続テスト」ボタンで、どこまで到達できたかを表示します。


## 通信方式（2026-10 変更）

- iPhone の Safari では **JSONP（`<script>` による取得）が動かない**ことを実機で確認したため、取得は **`fetch`（通常のHTTP）を本線**にしました。失敗したときだけ JSONP に切り替えます。
- 書き込みは **GET（URLに内容を載せる方式）** に変更しました。`POST` は Apps Script の転送（302）で**本文が失われる**ため、シートに1件も記録されない原因になっていました。GET は転送されても内容が保たれ、さらにサーバーからの「受理した」という応答を直接確認できます。
- 1回の送信が長くなりすぎないよう自動で分割します（最大5件／約5,000文字）。

## どの端末の情報が正しいか（2026-10 追加）

**スプレッドシートが唯一の基準（正）です。** 端末は「一時的な作業場」で、正しい情報は常にシート側にあります。

### 仕組み
- 各行に**サーバーが発行する版番号（seq）**が付きます。端末の時計は使いません（時計ずれの影響を受けません）。
- 端末は書き込むとき「自分が知っている版番号（baseSeq）」を添えて送ります。
  - サーバーの版番号が同じ → 受理し、新しい版番号を振り直して応答します（受理が確定します）。
  - サーバーの版番号が進んでいる → **上書きせず、他端末の内容を優先**し、競合として応答します。
- 競合した更新は捨てずに、シートの `conflicts` タブと端末の「競合の記録」に残ります。画面には「他の端末が先に更新していたため、○○ は他の端末の内容を優先しました」と表示されます。
- ⚙設定の「**サーバーの内容を正として取り込む**」で、この端末の表示をシートに合わせ直せます（端末ごとの食い違いを一発で解消）。

### 端末名
- ⚙設定 →「この端末の名前」に「フロントPC」「店長スマホ」などを入れておくと、どの端末が最後に更新したかが記録されます。

### 競合（同時更新）が起きたら
1. 画面の通知で、どの部屋が競合したかを確認
2. ⚙設定 →「競合の記録」の件数を確認（直近50件を端末に保持）
3. シートの `conflicts` タブで、変更前の内容（client）と採用した内容（server）を比較
4. 必要なら該当の部屋を開いて登録し直す（その内容が新しい版番号で保存されます）


## 更新はその都度シートへ／表示はシート基準（2026-10 追加）

- 部屋の登録・作業完了・異常事態・担当者リスト・写真の縮小は、**操作したその場でシートへ送信**します。送信後、**サーバーが受理したか（版番号）まで確認**します。
- 送信が確認できたら、**シートの内容を読み直して画面を更新**します。つまり画面は常に「シートにある情報」を表示します。
- 画面下の設定には「表示はスプレッドシートの内容です（基準＝シート）。最終取得 ○/○ ○:○○。」と出ます。
- 送れなかったときは「シートへ送信できませんでした（未送信 n件）」と表示し、次回の同期で自動再送します。
- 他の端末が先に更新していた場合は、その端末の内容を優先して画面に反映し、競合として記録します（内容は消えません）。


## 更新ボタン（2026-10 追加）

- 画面右上に「**↻ 更新**」ボタンを追加しました。押すと **シートの内容だけを読み込んで表示**します（この端末からは**送信しません**）。
- ⚙設定にも「**シートから最新を読み込む（更新）**」があります。「今すぐ同期（送信＋読込）」は、この端末の未送信の変更を送ってから読み込みます。
- 送信すべきものの判定は**端末の時計を使わず**、「この端末で変更した印（dirty）」で行います。時計がずれた端末でも、変更が送られない／古い内容で上書きされる事故が起きません。
- この端末に**未送信の変更がある部屋**は、他の端末の古い内容で上書きしません（送信して競合した時点で、サーバーを優先して解決します）。

# 配布物の更新ガイド

## このドキュメントの目的

My Tango Note の配布物(LP からダウンロードされる ZIP ファイル)を更新するときの手順をまとめたものです。作業頻度はそれほど高くないため、いざ更新するときに「あれ、どうやるんだっけ」とならないように書いてあります。

---

## 配布物の構造

### ユーザーが受け取るもの

LP(mytangonote.com)の「キットをダウンロード」ボタンから、以下の ZIP ファイルが配布されます。

```
MyTangoNote.zip
└── MyTangoNote/
    ├── 01.説明書.txt
    ├── 02.AI設定プロンプト.txt
    ├── 03.MyTangoNote.html
    └── 04.サンプルMyTangoNote.html
```

### リポジトリの構造

配布物のマスターは、以下のリポジトリに保管されています。

```
landing/                       # mytangonote.com の本体
├── MyTangoNote.zip            # 配布される ZIP
├── kit/                       # ZIP の展開・編集可能なマスター
│   ├── 01.説明書.txt
│   ├── 02.AI設定プロンプト.txt
│   ├── 03.MyTangoNote.html
│   └── 04.サンプルMyTangoNote.html
├── index.html                 # LP
├── help/                      # ヘルプページ
└── (その他 LP 関連...)

notebook/                      # キット本体のソース
├── index.html                 # 03.MyTangoNote.html のマスター
├── examples.html              # 04.サンプルMyTangoNote.html のマスター
└── (その他...)
```

### マスターと配布ファイルの対応表

| 配布物のファイル | 編集すべきマスター |
|---|---|
| `MyTangoNote/01.説明書.txt` | `landing/kit/01.説明書.txt` |
| `MyTangoNote/02.AI設定プロンプト.txt` | `landing/kit/02.AI設定プロンプト.txt` |
| `MyTangoNote/03.MyTangoNote.html` | `notebook/index.html` |
| `MyTangoNote/04.サンプルMyTangoNote.html` | `notebook/examples.html` |

注意: 03 と 04 は `notebook` repo に正本があり、`landing/kit/` の中身はリネームコピーです。修正は必ず `notebook` repo 側で行ってください。

---

## 更新手順

### シナリオ A: 01 や 02 を更新するとき(説明書 / AI プロンプト)

例: AI プロンプトに新しい注意点を追加したい

1. `landing` repo の `kit/01.説明書.txt` または `kit/02.AI設定プロンプト.txt` を GitHub Web UI で開く
2. 鉛筆アイコン(編集)をクリックして編集
3. Commit changes(コミットメッセージは変更内容を簡潔に)
4. → 後述の「シナリオ C: ZIP の再生成と差し替え」へ進む

### シナリオ B: 03 や 04 を更新するとき(キット本体の機能 / UI)

例: ノートのデザインを調整したい、新機能を追加したい

1. `notebook` repo の `index.html` または `examples.html` を編集して commit
2. 編集後の最新版を「Download raw file」でローカルにダウンロード
3. ダウンロードしたファイルをリネーム:
   - `index.html` → `03.MyTangoNote.html`
   - `examples.html` → `04.サンプルMyTangoNote.html`
4. `landing` repo の `kit/` フォルダに、既存のファイルを上書きする形でアップロード
5. → 後述の「シナリオ C: ZIP の再生成と差し替え」へ進む

### シナリオ C: 共通 — ZIP の再生成と差し替え

どのシナリオでも、kit/ を更新したあとは ZIP を再生成して差し替えます。

#### Step 1: ローカルで ZIP を作る

1. `landing` repo の `kit/` フォルダ内の最新ファイル 4 つをローカルにダウンロード
   - 簡単な方法: 「Code → Download ZIP」で repo 全体をダウンロード → kit/ フォルダだけ取り出す
   - または、ファイルを 1 つずつ Raw でダウンロードして集める
2. デスクトップに **`MyTangoNote`** という名前のフォルダを作り、その中に 4 ファイルを入れる
3. `MyTangoNote` フォルダを右クリック → 「"MyTangoNote" を圧縮」 → `MyTangoNote.zip` ができる

#### Step 2: 中身を確認

1. 作った `MyTangoNote.zip` をダブルクリックして展開
2. 展開された `MyTangoNote` フォルダの中に 4 ファイルが正しく入っているか確認
3. **隠しファイルが混入していないか確認**(これ重要):
   - Finder で `Cmd + Shift + .` (ピリオド)で隠しファイル表示
   - `.DS_Store` や `__MACOSX/` が見えたら削除して再圧縮

#### Step 3: landing repo の MyTangoNote.zip を差し替え

1. `landing` repo の既存の `MyTangoNote.zip` を開く → ゴミ箱アイコン(削除) → Commit
2. landing repo に戻る → Add file → Upload files
3. 新しい `MyTangoNote.zip` をドラッグ&ドロップ
4. Commit message に変更内容を記載
5. Commit changes

**なぜ「削除 → アップロード」の 2 コミット?**
GitHub Web UI で同名ファイルの上書きアップロードは「動くときと動かないとき」があるため、確実な方法を選んでいます。コミットが 2 つになりますが、履歴的にはむしろクリーン。

---

## デプロイ確認

1. commit から数分待つ(Cloudflare Pages の自動デプロイは通常 1〜3 分)
2. https://mytangonote.com にアクセス
3. 「キットをダウンロード」から MyTangoNote.zip をダウンロード
4. 展開して、変更したファイル(例: 02.AI設定プロンプト.txt)を開く
5. 変更が反映されているか目視確認

可能なら、新しい AI スレッドで実際にプロンプトを動かして、動作まで確認するとベスト。

---

## つまづきポイント

### 「圧縮した ZIP に変なファイルが入っている」

macOS の Finder で圧縮すると、`.DS_Store` や `__MACOSX/` フォルダが混入することがあります。

- 隠しファイル表示(`Cmd + Shift + .`)で確認
- 混入していたら削除して再圧縮

### 「Cloudflare Pages の反映が遅い」

通常 1〜3 分で反映されますが、5 分以上経っても変わらないときは:

- ブラウザのキャッシュをクリア(またはシークレットウィンドウで確認)
- それでもダメなら GitHub の commit が正しく入っているか確認

### 「過去のバージョンに戻したい」

GitHub の commit 履歴から該当のコミットを参照すれば、過去のファイル状態を確認できます。MyTangoNote.zip の各バージョンはコミットに紐づいているので、特定のコミットからファイルをダウンロード → 再アップロードすれば過去版に戻せます。

---

## 関連リポジトリ

- LP・配布物: https://github.com/mytangonote/landing
- キット本体ソース: https://github.com/mytangonote/notebook

---

最終更新: 2026/05/27

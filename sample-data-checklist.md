# サンプルデータ準備チェックリスト

**対象ワークショップ：** IBM Bob × COBOLマイグレーション体験ハンズオン
**ティア：** A（高リテラシ層）
**業種設定：** 証券会社向けシステムインテグレーション（架空顧客：東邦フィナンシャルシステムズ株式会社）

---

## 架空企業・シナリオ設定

| 項目 | 設定内容 |
|---|---|
| 企業名 | 東邦フィナンシャルシステムズ株式会社（架空） |
| 業種 | 証券会社向け SI（株式取引・勘定系・口座管理） |
| 規模 | 年商 3,200億円、従業員 2,800名、拠点 12か所 |
| 課題背景 | 口座管理バッチ群（1990年代実装）の保守開発者が全員退職。若手SEに引き継がれたが誰も仕様を把握していない。コンプライアンス要件の対応で年2〜3件の改修依頼が来るが、影響調査に2〜4週間かかっている。 |
| 依頼テーマ | 「信用スコア（CREDIT-SCORE）フィールド追加」影響調査と、既存バグの洗い出し |

---

## 使用するファイル一覧

本ワークショップでは **外部サンプルデータファイルを使わず、COBOL ソースコードそのものが「データ」** です。
以下のファイルをワークスペースに配置するだけで全Labが実施できます。

### 必須ファイル（7点）

| ファイル名 | 取得元 | 使用Lab | 配置場所 | 仕掛け |
|---|---|---|---|---|
| `CBL0001.cobol` | Course #2 / Labs/cbl | Lab 1 | ルート | なし（正常動作する基本形） |
| `CBL0010.cobol` | Course #2 / Labs/cbl | Lab 1 | ルート | なし（CBL0001の機能強化版） |
| `CBL0012.cobol` | Course #2 / Labs/cbl | Lab 2 | ルート | **バグ仕込み済み**（下記参照） |
| `CBL0033.cobol` | Course #2 / Labs/cbl | Lab 2 | ルート | **到達不能コードあり**（下記参照） |
| `EMPPAY.CBL` | Course #4 / Labs/cbl | Lab 3 | ルート | **論理バグ仕込み済み**（下記参照） |
| `DEPTPAY.CBL` | Course #4 / Labs/cbl | Lab 3 | ルート | **表示バグ仕込み済み**（下記参照） |
| `emppay.cut` | Course #4 / Labs/tests | Lab 3 | ルート | **テスト自体がバグを見逃す**（下記参照） |

### 参照用ファイル（任意）

| ファイル名 | 取得元 | 説明 |
|---|---|---|
| `CBL0001J.jcl` | Course #2 / Labs/jcl | JCLの説明用。実行はしない。 |
| `deptpay.cut` | Course #4 / Labs/tests | 発展課題用 |

---

## ファイル取得方法

```bash
# ローカルに cobol-programming-course-3.2.0 を展開済みの場合
SRC="/Users/hajimehongo/Downloads/cobol-programming-course-3.2.0"

cp "$SRC/COBOL Programming Course #2 - Learning COBOL/Labs/cbl/CBL0001.cobol" ./
cp "$SRC/COBOL Programming Course #2 - Learning COBOL/Labs/cbl/CBL0010.cobol" ./
cp "$SRC/COBOL Programming Course #2 - Learning COBOL/Labs/cbl/CBL0012.cobol" ./
cp "$SRC/COBOL Programming Course #2 - Learning COBOL/Labs/cbl/CBL0033.cobol" ./
cp "$SRC/COBOL Programming Course #4 - Testing/Labs/cbl/EMPPAY.CBL" ./
cp "$SRC/COBOL Programming Course #4 - Testing/Labs/cbl/DEPTPAY.CBL" ./
cp "$SRC/COBOL Programming Course #4 - Testing/Labs/tests/emppay.cut" ./
```

または GitHub から直接ダウンロード：
https://github.com/openmainframeproject/cobol-programming-course（CC-BY-4.0）

---

## 仕込んだ「答え」一覧（講師用・_INSTRUCTOR_NOTES.md と対応）

### Lab 2 で発見すべきバグ（CBL0012）

| # | 発見事項 | 証拠となる箇所 | 深刻度 | 位置づけ |
|---|---|---|---|---|
| 1 | `FUNCTION CURRENT-DATA` スペルミス（正: `CURRENT-DATE`） | CBL0012.cobol:118 | 高（コンパイル不可） | 必ず発見させる（最低ライン） |
| 2 | WS-CURRENT-TIME（時刻定義）の欠落 | CBL0012.cobol:104〜108 | 中（機能不全） | 上位者が気づく |
| 3 | LAST-NAME の MOVE 文がコメントアウト | CBL0012.cobol:163 | 中（出力に姓が出ない） | 余力がある人向け |

### Lab 2 で発見すべきバグ（CBL0033）

| # | 発見事項 | 証拠となる箇所 | 深刻度 | 位置づけ |
|---|---|---|---|---|
| 4 | GO TO後のDISPLAY文が到達不能コード | CBL0033.cobol:71 | 低（保守性） | 発展用 |

### Lab 3 で発見すべきバグ（EMPPAY / DEPTPAY / emppay.cut）

| # | 発見事項 | 証拠となる箇所 | 深刻度 | 位置づけ |
|---|---|---|---|---|
| 5 | IF条件順序バグ：EMP-HOURS=50でOT-RATEが0.25になる（正:0.50） | EMPPAY.CBL:32-37 | 高 | 必ず発見させる（最重要） |
| 6 | DISPLAY-DETAILS：MANAGER-LNAMEがMANAGER-FNAMEを表示 | DEPTPAY.CBL:33 | 中 | 上位者が気づく |
| 7 | 既存テストがバグを見逃す（EMP-HOURS=50のOT-RATEを0.25と期待） | emppay.cut:3-7 | 中 | 最重要の気づき |

**合格ライン：** 各Lab 2件以上発見  
**優秀ライン：** 各Lab 3件以上発見  
**全件（7件）発見：** まず出ない。褒める。

---

## フォルダ構造

```
workshops/jip-cobol-migration/
├── index.html                ← 受講者向け手順書（このファイルをブラウザで開く）
├── _INSTRUCTOR_NOTES.md      ← 講師用（受講者に配布しないこと）
├── sample-data-checklist.md  ← このファイル（講師用）
│
└── cobol-files/              ← 受講者に配布するCOBOLソース7点
    ├── CBL0001.cobol
    ├── CBL0010.cobol
    ├── CBL0012.cobol
    ├── CBL0033.cobol
    ├── EMPPAY.CBL
    ├── DEPTPAY.CBL
    └── emppay.cut
```

> ⚠️ `_INSTRUCTOR_NOTES.md` と `sample-data-checklist.md` は受講者配布フォルダに含めないこと。

---

## データの性質・ライセンス

本ワークショップで使用する COBOL ソースはすべて **Open Mainframe Project COBOL Programming Course** からのものです。

- ライセンス：**CC-BY-4.0**（クレジット表記のみで商用・改変可）
- オリジナル：https://github.com/openmainframeproject/cobol-programming-course
- バグは**意図的に設計されたもの**（教育目的）であり、実在するシステムのバグではありません

---

## 当日の配布準備チェックリスト

- [ ] `cobol-files/` フォルダに7ファイルが揃っているか確認
- [ ] `index.html` をブラウザで開いて表示確認
- [ ] `_INSTRUCTOR_NOTES.md` と `sample-data-checklist.md` が受講者用ZIPに含まれていないか確認
- [ ] 配布方法を決定（USB / 共有リンク / Bob のワークスペース直接配置）
- [ ] バックアップを別媒体に保存済みか確認

---

## サンプル候補の選定理由（背景）

今回の最終選定は **選択肢2（Open Mainframe Project COBOL Programming Course #2 + #4）** です。

| 候補 | 評価 | 理由 |
|---|---|---|
| ❌ **候補1** `github.com/IBM/cobol-is-fun` | 不採用 | Hello World相当のシンプルさ。影響調査・バグ修正・テスト自動化のシナリオ展開が困難。 |
| ✅ **候補2** Open Mainframe Course #2 + #4 | **採用** | 共通データモデル(ACCT-REC)、意図的バグ、段階的進化形、zUnitテスト付属。SE向け体験に最適。 |
| △ **候補3** ローカルの cobol-programming-course-3.2.0 | 同上 | 候補2と同一内容のローカルコピー。取得元として使用。 |

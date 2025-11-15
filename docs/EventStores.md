# EventStore リファレンス

ARTEMIS のイベントループは `IEventStore` を実装したプロセッサからデータを受け取り、`TLoop` 内で最初に見つかった EventStore から実行時の Run 情報を取得して `TAnalysisInfo` に書き戻します（`sources/loop/IEventStore.h`, `sources/loop/TLoop.cc` を参照）。本書では、ベース・インターフェースの概要と、リポジトリ内で提供されている主な EventStore の役割およびパラメータを一覧します。

## IEventStore

| 項目 | 説明 |
| --- | --- |
| 実装場所 | `sources/loop/IEventStore.h` |
| 役割 | EventStore が Run 番号／Run 名を Loop 全体に伝搬できるようにするための極小インターフェース。`TLoop` は最初に見つかった `IEventStore` 実装を保持し、`GetRunNumber` / `GetRunName` の戻り値を `TAnalysisInfo` に記録する。 |
| 提供 API | `Int_t GetRunNumber() const`（デフォルト 0）、`const char* GetRunName() const`（デフォルト空文字）。必要に応じて派生クラスで上書きする。 |

## ファイル／オンライン入力系 EventStore

### TRIDFEventStore (`sources/loop/ridf/TRIDFEventStore.{h,cc}`)

- **概要**: RIDF ファイルまたは共有メモリからオンライン／オフラインでデータを取り出し、`TSegmentedData`・`TEventHeader`・Run ヘッダ一覧・スケーラ情報を構築する標準 EventStore。クラス ID ごとのデコーダ関数を保持し、タイムスタンプ・スケーラ・コメントブロックにも対応します。
- **特徴**:
  - 入力ファイルが無い場合はオンラインモードとして共有メモリを利用（`SHMID` で制御）。
  - `InputEventNumber`/`OutputEventNumber` を Info として登録することで、複数 EventStore 間でイベント番号を同期可能。
  - `EventListName` を指定すると `TTimestampEventList` に従った再生が行える。
  - MPI が有効な場合はプロセス毎に役割を分担。
- **主要パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `InputFiles` | `StringVec_t`（必須） | 解析対象の RIDF ファイル列。空の場合はオンライン入力と見なす。 |
| `SegmentedData` | 出力コレクション、既定値 `"segdata"` | 下流プロセッサに渡す `TSegmentedData`。 |
| `RunHeadersName` | 出力 Info、既定値 `"runheader"` | `TRunInfo` を蓄える `TList`。ループ開始時に 1 回登録。 |
| `EventHeaderName` | 出力コレクション、既定値 `"eventheader"` | `TEventHeader` へのアクセス名。 |
| `InputEventNumber` | 入力 Info、既定値 `""` | 他の EventStore とイベント番号を合わせるための `TSimpleDataLong`。`SetDoAuto(true)` により自動取得。 |
| `OutputEventNumber` | 出力 Info、既定値 `""` | 本 EventStore のイベント番号を Info として公開。 |
| `MaxEventNum` | `Int_t`（0） | 0 で無制限、正数で最大イベント数に達するとループ終了。 |
| `SHMID` | `Int_t`（0） | オンライン入力で利用する共有メモリ ID。 |
| `Start` | `Int_t`（0） | 処理を開始するイベント番号。 |
| `Asynchronous` | `Int_t`（0） | タイムスタンプ再構築時に非同期ラン終了を許可するフラグ。 |
| `EventListName` | 入力 Info、既定値空文字 | 使用する `TTimestampEventList` の Info 名。 |

### TRCNPEventStore_ts (`artemis-share/src/rcnp/TRCNPEventStore_ts.{h,cc}`)

- **概要**: RCNP 実験のタイムスタンプ付き RIDF 形式を扱う EventStore。`TRCNPEventHeader` を用いたランヘッダの構築や、RCNP 固有のブロックヘッダ (`bld1_header`) 解析を含む。
- **主要パラメータ**: RIDF 版と同様に `InputFiles`、`SegmentedData`（既定 `"segdata"`）、`RunHeadersName`（既定 `"runheader"`）、`EventHeaderName`（既定 `"eventheader"`）、`SHMID`（共有メモリ ID、既定 0）をサポート。
- **補足**: イベントセグメントのクラスデコーダ (`ClassDecoder03/04/05/06`) を内部で切り替え、RCNP 独自のコメント／タイムスタンプ形式に合わせて `TSegmentedData` を構築します。

### TGetEventStore (`sources/loop/get/TGetEventStore.{h,cc}`)

- **概要**: GET (General Electronics for TPC) の COBO/ASAD フレームを `GETDecoder` で展開し、`TSegmentedData` に詰める EventStore。FPN 取得やヒットビット判定、MPI でのファイル分散に対応します。
- **主な I/O**: 出力 `SegmentedData`（既定 `"segdata"`）、`EventHeaderName`（既定 `"eventheader"`）、`RunHeadersName`（既定 `"runheader"`）。
- **主要パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `InputFiles` | `StringVec_t`（必須） | `ls -tr <pattern>*` で展開される COBO ファイル群。 |
| `StartEventNum` | `Int_t`（0） | イベントカウンタの開始番号。 |
| `MaxEventNum` | `Int_t`（0） | 上限 0 で無制限。 |
| `RequireHitBit` | `Int_t`（1） | 1 でヒットビット必須、0 で通過。 |
| `SubtractFPN` | `Bool_t`（`kFALSE`） | FPN (固定パターンノイズ) を減算するか。 |
| `ValidBucket` | `IntVec_t(2,0)` | 有効バケツ範囲 [start, end]。0/0 の場合は `Init` 内で 0–512 に展開。 |
| `IsReducedCobo` | `Parameter<bool>`（`false`） | Reduced COBO 形式かどうか。true の場合 `ProcessReducedCobo()` を使用。 |

### TTreeEventStore (`sources/loop/TTreeEventStore.{h,cc}`)

- **概要**: ROOT TTree をイベントソースとして扱い、全ブランチを `TEventCollection` に自動登録する EventStore。MPI 環境ではプロセスごとにファイルリストを分割して `TChain` を構築します。
- **主な I/O**: `FileName` で指定した ROOT ファイル群の `TreeName` を読み出し、各ブランチを `TEventCollection` に登録。`TEventHeader` ブランチを見つければ `GetRunNumber`/`GetRunName` の情報源とする。
- **主要パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `FileName` | `TString`（`"temp.root"`） | 入力 ROOT ファイル。ワイルドカードはシェル `ls -tr` に委譲。 |
| `TreeName` | `TString`（`"tree"`） | 読み出す TTree 名。 |
| `MaxEventNum` | `Long_t`（0） | 0 で全イベント、正数で最大件数。 |

### TRDFEventStore (`sources/loop/rdf/TRDFEventStore.{h,cc}`)

- **概要**: RDF 形式 (RIDF から ROOT Data Format へ変換したもの) を読み出し、セグメント情報 (`TSegmentInfo`) とモジュール種別 (`TModuleType`) に基づいて `TModuleDecoderFactory` から適切なデコーダを取得して `TSegmentedData` を生成する EventStore。
- **主な I/O**: `SegmentedData`（既定 `"segdata"`）、`RunHeadersName`（既定 `"runheader"`）、`EventHeaderName`（既定 `"eventheader"`）。
- **主要パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `InputFiles` | `StringVec_t` | RDF ファイル一覧。 |
| `SegmentedData` | 出力コレクション（`TSegmentedData`） | デコーダ結果を格納。 |
| `RunHeadersName` | 出力 Info | `TRunInfo` の `TList` を公開。 |
| `EventHeaderName` | 出力コレクション | イベントヘッダ。 |
| `SegmentInfoName` | 入力 Info（既定 `"seglist"`） | `TClonesArray<art::TSegmentInfo>` を参照してセグメント定義を取得。 |
| `ModuleListName` | 入力 Info（既定 `"modlist"`） | `TClonesArray<art::TModuleType>` に記載されたデコーダ ID を利用。 |

## ストリーミング EventStore

### TStreamingEventStore (`sources/loop/streaming/TStreamingEventStore.{h,cc}`)

- **概要**: 旧フォーマット向けのストリーミング EventStore。Time Frame／Sub Time Frame／Heartbeat Frame などのヘッダを `TStreamingHeaderTF/STF/FS` で解釈し、`TSegmentedData`・Run 情報・イベントヘッダを構築します。スタンドアロン入力を想定したパラメータを多く持ち、最小構成でストリーミングデータを読む場合に利用します。
- **主な機能**:
  - `GetTimeFrame`/`GetSubTimeFrame`/`GetHeartBeatFrame` で各フレームを順に読み解き、Heartbeat 検知時にはイベント番号のみ進める。
  - `SegmentedData` 出力を `OutputData<TSegmentedData>` として公開し、`SetDoAuto(false)` を指定すれば既存配列を流用可能。
- **主要パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `SegmentedData` | 出力データ、既定名 `"segdata"` | 既存配列を共有するか、新たに生成。 |
| `RunHeadersName` | 出力 Info、既定 `"runheader"` | Run ヘッダの `TList`。 |
| `EventHeaderName` | 出力データ、既定 `"eventheader"` | `TEventHeader`。 |
| `MaxFrames` | `Parameter<Int_t>`（0） | 0 で無制限。フレーム数超過でループ停止。 |
| `StartFrame` | `Parameter<Int_t>`（0） | イベント番号の初期値。 |
| `FileName` | `Parameter<StringVec_t>` | 入力ストリームファイル群。`ls -tr` で解決。 |
| `FEMID` / `FEMType` | `Parameter<Int_t>`（0 / 2） | フロントエンド識別子。スタンドアロンモードで使用。 |
| `IsStandAlone` | `Parameter<Int_t>`（0） | 1 でスタンドアロン入力（ヘッダ無しのサンプラ出力）を想定。 |
| `DefaultLength` | `Parameter<Int_t>`（256） | スタンドアロン時の既定フレーム長 (kB)。 |

### art::v1::TStreamingEventStore (`sources/loop/streaming-v1/TStreamingEventStoreV1.{h,cc}`)

- **概要**: 新バージョン (v1) のストリーミング形式向け EventStore。旧来のパラメータを継承しつつ、オンライン解析ワークフローを意識した ZMQ／Redis を介した URI 解決や NestDAQ チャネル指定が追加されています。`GetRunNumber`/`GetRunName` は Run 情報が無い場合に備えてフォールバック値を返すよう強化されました。
- **追加パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `ZmqURI` | `Parameter<TString>`（空文字） | ZMQ を利用する場合の URI。 |
| `IsOnline` | `Parameter<int>`（0） | 1 でオンライン（ZMQ）モード。 |
| `RedisURI` | `Parameter<TString>`（`tcp://127.0.0.1:6379/0`） | Redis から ZMQ URI を解決する際の接続先（`HAVE_REDIS_H` が定義されている場合のみ有効）。 |
| `DeviceID` | `Parameter<TString>`（`TimeFrameSlicerByLogicTiming`） | NestDAQ プロセス名。 |
| `ChannelName` | `Parameter<TString>`（`dqm`） | NestDAQ チャネル。 |
| `SubChannel` | `Parameter<TString>`（`0`） | チャネル内のサブチャンネル。 |

## シミュレーション／補助 EventStore

### TRandomNumberEventStore (`sources/loop/TRandomNumberEventStore.{h,cc}`)

- **概要**: 一様乱数を `TSimpleData` として生成し続ける最小構成の EventStore。`example/rndmEventStore.yaml` のように、簡易ステアリングのテストベンチとして利用できます。
- **パラメータ**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `OutputCollection` | 出力コレクション名（`"random"`） | `TSimpleData` を公開する `TEventCollection` 上の名前。 |
| `MaxLoop` | `Int_t`（100） | 生成イベント数。到達すると `SetStopLoop()` で終了。 |
| `Min` / `Max` | `Float_t`（0 / 1） | 一様乱数の範囲。 |

### TBinaryReactionGenerator (`sources/mc/TBinaryReactionGenerator.{h,cc}`)

- **概要**: 二体反応のモンテカルロ生成器で、`IEventStore` を実装して Run 情報を提供します。`TArtParticle` の `TClonesArray` を 2 種（生成粒子と真値）として出力し、角度・励起エネルギー分布を指定可能。
- **パラメータ例**:

| 名前 | 種別/既定値 | 説明 |
| --- | --- | --- |
| `OutputCollection` / `MCTruthCollection` | 出力コレクション名（`"recoil"` / `"mctruth"`） | それぞれ生成粒子と MC 真値を格納する `TClonesArray`。 |
| `MaxLoop` | `Int_t`（1e6） | 生成イベント数。 |
| `Particle1/2/3` | `IntVec_t`（既定 `[1,1]`） | 質量数と原子番号。 |
| `KinMean` | `Float_t`（100） | 入射粒子の核子当たり運動エネルギー (MeV/u)。 |
| `ExRange` / `ExMean` / `ExWidth` | `FloatVec_t` or `Float_t` | 励起エネルギー範囲と分布。幅 0 でデルタ関数。 |
| `AngRange` / `AngMom` / `AngDistFile` | 角度分布設定 | Bessel 分布または外部ファイルによる角度分布。 |
| `DoRandomizePhi` | `Int_t`（1） | 方位角 φ を一様化するか。 |
| `RunName` / `RunNumber` | `Parameter<TString>` / `Parameter<Int_t>` | `IEventStore` が報告する Run 情報。 |

### TCounterEventStore (`sources/loop/TCounterEventStore.{h,cc}`)

- **概要**: 入力データを持たず、`MaxLoop` 回の `Process` 実行後に `TLoop::kStopLoop` と `kEndOfRun` を立ててループを終了させる単純な EventStore 互換プロセッサ。自前のテストチェーンでイベント数を制御したい場合に利用します。
- **パラメータ**: `MaxLoop` (`Int_t`, 既定 100)。


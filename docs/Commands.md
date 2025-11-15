# Artemis プロンプトで利用できるコマンド

`artemis` を起動すると `TCatCmdFactory` が `example/artemislogon.C` などで指示された `TCatCmd` 派生コマンドを登録し、プロンプトから直接呼び出せるようにします。ここでは `sources/commands`、`artemis-share/src/command`、`cat-src` に実装されているコマンドを名前順に整理しました。 【F:example/artemislogon.C†L10-L36】

| コマンド | コマンドクラス | 概要 |
| --- | --- | --- |
| `add` | `TCatCmdLoopAdd` | art::TLoopManager に新しいループを追加する 【F:sources/commands/TCatCmdLoopAdd.cc†L27-L28】 |
| `automacro` | `TCatCmdMacro` | 読み込んだマクロを自動的に実行する 【F:sources/commands/TCatCmdMacro.cc†L18-L19】 |
| `avx` | `TCatCmdAvx` | Y 軸方向でスライスしガウス関数でフィットする（FitSlicesY） 【F:sources/commands/TCatCmdAvx.cc†L16-L17】 |
| `avy` | `TCatCmdAvy` | X 軸方向でスライスしガウス関数でフィットする（FitSlicesX） 【F:sources/commands/TCatCmdAvy.cc†L16-L17】 |
| `bnx` | `TCatCmdBnx` | バンド射影を行う 【F:sources/commands/TCatCmdBnx.cc†L15-L16】 |
| `bny` | `TCatCmdBny` | Y 軸へのバンド射影を行う 【F:sources/commands/TCatCmdBny.cc†L15-L16】 |
| `branchinfo` | `art::TCmdBranchInfo` | ブランチ一覧やブランチのクラス情報を表示する 【F:sources/commands/TCmdBranchInfo.cc†L31-L32】 |
| `cd` | `TCatCmdCd` | カレントディレクトリを変更する 【F:sources/commands/TCatCmdCd.cc†L17-L18】 |
| `classinfo` | `art::TCmdClassInfo` | クラス情報を表示する 【F:sources/commands/TCmdClassInfo.cc†L48-L49】 |
| `comment` | `art::TCmdComment` | パッドにコメントを書く 【F:sources/commands/TCmdComment.cc†L24-L25】 |
| `fcd` | `art::TCmdFileCd` | ファイル内のディレクトリに移動する 【F:sources/commands/TCmdFileCd.cc†L25-L26】 |
| `figa` | `art::TCmdFiga` | ヒストグラムをガウス関数でフィットする 【F:artemis-share/src/command/TCmdFiga.cc†L25-L26】 |
| `fipo` | `art::TCmdFipo` | ヒストグラムを多項式関数でフィットする 【F:sources/commands/TCmdFipo.cc†L28-L29】 |
| `fls` | `art::TCmdFileLs` | ファイル内のオブジェクト一覧を表示する 【F:sources/commands/TCmdFileLs.cc†L24-L25】 |
| `gcom` | `art::TCmdGlobalComment` | artcanvas のグローバルヘッダーにコメントを追加する 【F:sources/commands/TCmdGlobalComment.cc†L19-L20】 |
| `hb` | `TCatCmdHb` | 次のヒストグラムを描画する 【F:sources/commands/TCatCmdHb.cc†L18-L19】 |
| `hcol` | `art::TCmdHcol` | 1 次元ヒストグラムの線色を変更する 【F:sources/commands/TCmdHcol.cc†L22-L23】 |
| `hdel` | `art::TCmdHdel` | カレントディレクトリ内のオブジェクトを削除する 【F:sources/commands/TCmdHdel.cc†L23-L24】 |
| `help` | `TCatCmdHelp` | ヘルプを表示する 【F:sources/commands/TCatCmdHelp.cc†L17-L18】 |
| `hn` | `TCatCmdHn` | 次のヒストグラムを描画する 【F:sources/commands/TCatCmdHn.cc†L18-L19】 |
| `hstore` | `TCatCmdHstore` | カレントディレクトリのヒストグラムを保存する 【F:sources/commands/TCatCmdHstore.cc†L20-L21】 |
| `ht` | `TCatCmdHt` | ヒストグラムを描画する 【F:sources/commands/TCatCmdHt.cc†L23-L24】 |
| `htp` | `TCatCmdHtp` | 現在のヒストグラムを描画する 【F:sources/commands/TCatCmdHtp.cc†L18-L19】 |
| `lgx` / `lgy` / `lgz` | `TCatCmdLg` | 現在または選択したパッドの指定軸を対数スケールにする 【F:sources/commands/TCatCmdLg.cc†L16-L43】 |
| `listg` | `art::TCatCmdListg` | ゲートの一覧を表示する 【F:sources/commands/TCatCmdListg.cc†L24-L25】 |
| `lnx` / `lny` / `lnz` | `TCatCmdLg` | 現在または選択したパッドの指定軸を線形スケールに戻す 【F:sources/commands/TCatCmdLg.cc†L16-L45】 |
| `ls` | `TCatCmdLs` | オブジェクト一覧を表示する 【F:sources/commands/TCatCmdLs.cc†L19-L20】 |
| `mergefile` | `art::TCmdMergeFile` | ヒストグラムファイルをマージする 【F:sources/commands/TCmdMergeFile.cc†L22-L23】 |
| `mnx` | `TCatCmdMnx` | X 軸へのプロフィールを作成する 【F:sources/commands/TCatCmdMnx.cc†L16-L17】 |
| `mny` | `TCatCmdMny` | Y 軸へのプロフィールを作成する 【F:sources/commands/TCatCmdMny.cc†L16-L17】 |
| `mpol` | `art::TCmdMpol` | マウス操作で多項式フィットを行う 【F:sources/commands/TCmdMpol.cc†L35-L36】 |
| `mwdccalib` | `art::TCmdMWDCCalib` | MWDC キャリブレータを起動する 【F:artemis-share/src/command/TCmdMWDCCalib.cc†L23-L24】 |
| `mwdcconfig` | `art::TCmdMWDCConfig` | MWDC コンフィギュレータを起動する 【F:artemis-share/src/command/TCmdMWDCConfig.cc†L25-L26】 |
| `pb` | `art::TCmdPb` | 前のサブパッドを選択する 【F:sources/commands/TCmdPb.cc†L22-L23】 |
| `pcd` | `art::TCmdPcd` | パッドを選択する 【F:sources/commands/TCmdPcd.cc†L22-L23】 |
| `pn` | `art::TCmdPn` | 次のサブパッドを選択する 【F:sources/commands/TCmdPn.cc†L22-L23】 |
| `print` | `art::TCmdPrint` | 最後に保存した図を出力する 【F:sources/commands/TCmdPrint.cc†L25-L26】 |
| `processordescription` | `art::TCmdProcessorDescription` | PrintDescriptionYAML を表示する 【F:sources/commands/TCmdProcessorDescription.cc†L24-L25】 |
| `prx` | `TCatCmdPrx` | X 軸へ射影する 【F:sources/commands/TCatCmdPrx.cc†L16-L17】 |
| `pry` | `TCatCmdPry` | Y 軸へ射影する 【F:sources/commands/TCatCmdPry.cc†L16-L17】 |
| `pzoom` | `art::TCmdPadZoom` | サブパッドをズームする 【F:sources/commands/TCmdPadZoom.cc†L27-L28】 |
| `resume` | `TCatCmdLoopResume` | ループを再開する（現在は 1 番目のみ） 【F:sources/commands/TCatCmdLoopResume.cc†L18-L19】 |
| `rgx` / `rgy` / `rgz` | `art::TCmdRg` | 選択したヒストグラムの指定軸レンジを設定する 【F:sources/commands/TCmdRg.cc†L22-L77】 |
| `save` | `TCatCmdSave` | キャンバスをファイルへ保存する 【F:sources/commands/TCatCmdSave.cc†L21-L22】 |
| `save` | `art::TCmdSave` | キャンバスをファイルへ保存する 【F:sources/commands/TCmdSave.cc†L25-L26】 |
| `slo` | `art::TCmdSlope` | マウスで傾きを取得する 【F:sources/commands/TCmdSlope.cc†L32-L33】 |
| `sly` | `TCatCmdSly` | Y 軸方向へのスライス射影を行う 【F:sources/commands/TCatCmdSly.cc†L17-L18】 |
| `suspend` | `TCatCmdLoopSuspend` | ループを一時停止する（現在は 1 番目のみ） 【F:sources/commands/TCatCmdLoopSuspend.cc†L18-L19】 |
| `terminate` | `TCatCmdLoopTerminate` | ループを終了する（現在は 1 番目のみ） 【F:sources/commands/TCatCmdLoopTerminate.cc†L18-L19】 |
| `unzoom` | `art::TCmdUnZoom` | 現在のヒストグラムのズームを解除する 【F:sources/commands/TCmdUnZoom.cc†L26-L27】 |
| `updatecanvas` | `art::TCmdUpdateCanvas` | キャンバスと子要素を 1 回または自動で更新する 【F:sources/commands/TCmdUpdateCanvas.cc†L27-L28】 |
| `upload` | `art::TCmdUpload` | 画像ファイルを GitLab API 経由でアップロードする 【F:sources/commands/TCmdUpload.cc†L29-L30】 |
| `xsta` | `TCmdXsta` | マウスで選んだ範囲のエントリ数を取得する 【F:cat-src/TCmdXsta.cc†L31-L32】 |
| `xval` | `TCatCmdXval` | マウスで座標値を取得する 【F:sources/commands/TCatCmdXval.cc†L23-L24】 |
| `zone` | `TCatCmdZone` | キャンバスを分割する 【F:sources/commands/TCatCmdZone.cc†L17-L18】 |

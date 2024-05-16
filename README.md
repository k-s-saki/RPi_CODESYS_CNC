# RPi_CODESYS_CNC

![image](https://github.com/k-s-saki/RPi_CODESYS_CNC/assets/30764977/b74649d9-26e1-434d-a580-865c8b4031f2)

Raspberry Pi + CODESYS(SoftMotion CNC/ROBOTICS) でどこまで汎用の(FANUCやSIEMENS等の）CNCに近いものが作れるでしょうか。

現時点では、EtherCATを想定しています。
サーボドライブが無くても実行・検証できるよう、仮想EtherCATドライブで実行できるようにしています。

機材が手に入れば、RPi+EtherCATサーボドライブでテストしてみようと思います。

（　EtherCATデバイス（ACサーボドライブの機材など）で、もし不要なものがありましたら、譲渡・貸与頂けると、とても助かります。　）

---

## プロジェクトの目的

![image](https://github.com/k-s-saki/RPi_CODESYS_CNC/assets/30764977/3e141b88-2620-4469-a158-ec25fdf65459)

CODESYSのCNCは、DIN66025のGコードをサポートします。ただ、マシニングセンタなどに搭載されている、一般的な汎用のCNCに比べると必要な（あってほしい）機能が無かったりします。

DIN66025についてはこちら
https://help.codesys.com/webapp/_sm_cnc_din66025_basics;product=codesys_softmotion;version=4.6.0.0

CODESYSの「CNC機能」を使って機械システムを作ろうとしたときに、DIN66025にはない、汎用の機械のGコード（FANUCやSIEMENSのCNCのGコード）をどのように実装していいのか、そもそも出来るのか、出来ないのか、ヘルプやマニュアル等ではよくわからないのが実情です。
Gコードの長さなどは、使用するPLCのメモリによって制限され、同時制御できるサーボ軸数の最大数はPLCのCPU速度などで変わったりします。

良い点としては、CODESYSのCNCやソフトモーションを使うことで

1. 6万以上のMコードをユーザーが自由に使用して拡張ができ、２つの浮動小数のパラメータを渡すことができます。
2. Gコード内にキーワードを置いて、実行中のCODESYS内の変数で置換して使用することができます。
3. Raspberry PiやPCシミュレーターで開発をすすめ、最後にPLCに置き換えることができます。


CODESYSだけを使い、CNCのGコードの動作、ラダー等のプログラムの連携のシミュレーションができます。
CODESYSをマスターするだけで、付加軸や追加のPLCで簡易的なロボットも動作させ、連携することが簡単です。


---

### 拡張実装の方針

DIN66025にない普通のCNCマシニングセンタで使っているのGコードは、PC側で単純なMコードへの置換をすることで、CODESYSのCNCで動作させることができます。

例えば、主軸回転数指令は、DIN66025にはありませんが、MコードにKパラメータを指定して、CODESYSのPOUの変数で受け取る事が出来ます。ここまで出来ていれば(このプロジェクトでは出来ています）、ユーザーの機械側のラダーで、主軸の制御にあわせてD/AやEhterCATのインバーターに回転数になるように値を書き込めばいいだけです。

一般的なCAD/CAMでS指令を主軸回転数の指定に利用していれば、送信前にS12345 を M60001 K12345に（テキストエディタなどで）単純に置換するだけです。拡張機能はM60000以上をCODESYSで利用する計画です。

主軸の回転指定後は、ものすごく加速性能の高い主軸なら、指令後にすぐに次にいってもいいし、主軸の回転数が上がるまでタイムラグが大きいものは、指定回転数を確認して次に行く、など、機械に応じてCODESYS側で制御します。
CNCで加工する対象が、回転主軸とは限らないかもですし、センサーや測長デバイス、A/Dで、計測をしたい場合もあります。（計測の場合はファイルを作る、書き込むことになります。）

一般的にFAの制御装置でコントロール等可能なもの、RPiやPLCへの入出力が可能ならば、Mコードにすることで、このようなことが可能です。


---

### 現在の実装　＝　CNCとして動作確認している事

・4000行の3D-CAD出力のXYZのG-Code（NURBS曲線を微小線分に分割）したものを、滑らかに補間しながら動作させる
・Mコードデバッグの機能（エラー時、終了時）
・DCSオフセット座標（ワーク座標）のリアルタイム表示
・主軸回転数の機能　（GコードのMコード化＋オーバーライド動作）


### CNCとしての制限や拡張を検討していること

・一般的によくあるMコードをとりあえず入れる予定
・Gコードの容量は4000行として、POU:CNCに宣言していますが、PLCにあわせて変更可能です。こういったパラメータの文書化。
・Mコードをサーボ制御タスクではなくて、ユーザーのタスク(10ms-20ms等）で制御できる工夫。👆現時点では、ユーザータスクでMコードを直接処理すると、Mコードが連続するときにMFINの同期の問題が出ます。

・工具長・径補正のテーブルを作り、補正テーブル番号で補正値を指定すること。
・DCSテーブルを作り、テーブル番号でDCSを指定すること。


---

### 本プロジェクトが貢献できること

CODESYSのMotion系のFBには、様々なものがあり、それぞれの機能がありますが、マニュアルやＷＥＢのヘルプを見ても、なかなか意味が分かりません。

また、必要なFBを理解できても、複数のFBをどのように組み合わせれば必要な機能が実現できるのか、UIとFBとGコードの組み合わせ、実験、作りこみにも間と手間がかかります。

CNCの内部ブロック的な理解をした上で、プログラムを作って、調整をしていく必要があります。それから、正しく動くことだけでなく、性能や容量の限界などを実機のメモリやCPU等の限界とあわせて調整していくことになります。

本プロジェクトを使えば、これらの負担が大幅に削減できると思います。

本プロジェクトでは、汎用のCNCに近いものをRPiとCODESYSで実装し、どこまで実現できるのか、どこまで性能が発揮できるのか、仮想機能や実機でテストできるもののベースとなって、ユーザーの作りこみ、性能や安定性の実用のベースになるものになるプロジェクトを目指しています。


### 中小企業で使えるCNC、学習や研究用途でも使えるCNCを

RPiを使った本プロジェクトでは、無料で2時間機械は稼働できますので、機械の試作、立ち上げ、教育などで気軽に使えます。

（　本格的なPLCやモーションコントローラを使った連続稼働には、CODESYSの必要なライセンス購入が必要です。　）

本プロジェクトをCAD/CAM/CNCやCODESYSの学習、中小企業のAIやDXの活用、人手不足に役立てて頂けたら幸いです。


### CODESYSの利点と日本企業のFA機材の利点

本プロジェクトと同様のCODESYSを使っていれば、本プロジェクトから不必要な画面や機能、使用しないサーボ軸等を削除し、FA制御機器各社の高度なPLCやモーションコントローラに対応し、各機械にあわせたものにするのは簡単ではないかと思います。

個人的にCODESYS対応のPLCに移植を始めていますが、プロジェクトでデバイスを変更すると、PLCのCODESYSのバージョンが古いものを使う必要があるので、エラーが出る所がほんの少しありますが、修正は簡単です。

これだけの機能が、PCとIoTをベースとしてRPiでプロトタイプや検証をおこなったあと、信頼・実績があり、保守やサポートの手厚い日本企業のPLCに簡単に変換し調整でき、加えて各社のサーボモータ・ステッピングモータを、容量、構造などを問わず、自由に適切に組み合わせて使えるのは驚きです。


![image](https://github.com/k-s-saki/RPi_CODESYS_CNC/assets/30764977/ac96e098-c7c2-49ae-88b3-adaee331c296)

![image](https://github.com/k-s-saki/RPi_CODESYS_CNC/assets/30764977/e1da095e-0aab-4cf0-a5ab-36eebf843178)

# RPi_CODESYS

Raspberry Pi + CODESYS(SoftMotion CNC/ROBOTICS) + EtherCAT でどこまで汎用のCNCが作れるか挑戦しています。

現時点では、EtherCATのサーボドライブが無くても実行・検証できるよう、仮想EtherCATドライブで実行できるようにしています。

PCで仮想サーボドライブで正しく実装されたら、RPi+EtherCATサーボドライブでテストしてみようと思います。

EtherCATデバイス（ACサーボドライブの機材など）は高価なので、もし不要なものがありましたら、譲渡・貸与をお願い致します。

---

CODESYSのCNCは、DIN66025のGコードをサポートしますが、機能がやや限られていて、FANUCやSIEMENSのCNCの機能（使い勝手）には遠く及びません。

DIN66025
https://help.codesys.com/webapp/_sm_cnc_din66025_basics;product=codesys_softmotion;version=4.6.0.0

基本的な機能はFBがありますが、UIとFBの組み合わせ、作りこみ、テストにかなり工数がかかります。

汎用のCNCに近いものをCODESYSで実現するべく、本プロジェクトでは、どこまで実現できるのか、どこまで性能が発揮できるのか、実装、テストして、性能や安定性の実用のベースになるものになる実装を目指しています。






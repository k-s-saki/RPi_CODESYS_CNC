# RPi_CODESYS

Raspberry Pi + CODESYS(SoftMotion CNC/ROBOTICS) + EtherCAT でどこまで汎用の仮想CNCが作れるか挑戦しています。

CODESYSのCNCは、DIN66025のGコードをサポートしますが、機能がやや限られていて、FANUCやSIEMENSのCNCの機能（使い勝手）には遠く及びません。
基本的な機能はFBがありますが、UIとFBの組み合わせ、作りこみがなかなか大変です。

DIN66025
https://help.codesys.com/webapp/_sm_cnc_din66025_basics;product=codesys_softmotion;version=4.6.0.0

汎用のCNCに近いものをCODESYSで実現したいのですが、本プロジェクトでは、どこまで実現できるのか、どこまで性能が発揮できるのか、実装、テストして、性能や安定性の実用のベースになるものになる実装を目指しています。。

EtherCATのサーボドライブが無くても実行・検証できるよう、仮想EtherCATドライブで実行できるようにしています。





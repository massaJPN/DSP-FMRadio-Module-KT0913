# DSP-FMRADIO-MODULE-KT0913
https://massa4649.booth.pm/items/5619730 の製品説明となります。
## 概要
モジュールを制御するマイコンとのインターフェース用の端子配列は、M5StickC-PLUSのコネクタに直刺しで動作させることを考慮した設計にしています。
また、FM受信アンテナは、Φ3.5mmステレオミニジャックに接続するイアホンやオーディオケーブルのGNDラインを利用する仕様となっています。

![outline](https://github.com/user-attachments/assets/af2f896b-25dd-4cde-97a1-be9d29b833bc)

## 選局/周波数変更
モジュールの4番端子はKT0913のチップイネーブル端子の9番ピンに接続されています。I2C制御を開始する前にHighにすることが必要です。
受信チャンネルの選局方法については、I2C制御でKT0913の周波数に関するレジスタ設定値を変更することで周波数を上下に可変できます。

## 音量調節
音量の調節についても、I2C制御でKT0913の音量に関するレジスタ設定値を変更することで可変できます。

## その他
2024年9月現在においては、KT0913は電子部品ショップの秋月電子さんでも販売されていますので、そちらのWEBサイトにデータシートがありますのでご参照下さい。

モジュールに実装されている水晶振動子は、デフォルトのレジスタ設定で動作するように32.768KHzを使用しています。

## 外観
![module-50](https://github.com/user-attachments/assets/b03bb9ca-ef04-4d56-9fec-cab94e46d106)

## 端子配列
|Pin|Pin Name|I/O|Function|
|-----|-----|-----|-----|
|1|GND|Ground|Ground|
|2|NC|-|Non Connection|
|3|SCL|Digital I/O|SCL of 2-wire interface|
|4|EN|Input|Chip enable. internal 600kΩ Pulldown (※connected to pin9 of KT0913)|
|5|SDA|Digital I/O|SDA of 2-wire interface|
|6|NC|-|Non Connection|
|7|+3.3V|Power|Power supply (※2.1～3.6V)|
|8|NC|-|Non Connection|

## 使用例

### M5stickC Plus でコントロール

可変周波数範囲はICのレジスタ設定で決定します。（ICの機能としては32MHz～110MHzの受信が可能）  
プログラミング次第ですが、周波数変更の手段としてM5stickC PlusのボタンAとボタンBを使用できます。  
サンプルプログラムではAボタンで100kHz周波数がアップします。  
あらかじめ受信可能なラジオ曲の周波数をプリセットとしてプログラムに配列で定義すれば、ボタンAを押すとプリセットされた次の局へ進み、ボタンBで前の局へ戻るようなプログラムの実装も可能です。
```
  if (M5.BtnA.wasPressed() && dial == 0 && AM_FM == "FM") {
    REGISTER_ADDRESS = TUNE;
    I2CreadMULTIbyte();
    data = (data + 0x0002) | 0B1000000000000000; // + 100KHz step   = 100/50  76MHz - 94.1MHz
    if ( (data & 0B0000111111111111)  > 1882){
      data = 0B1000010111110000; // 76MHz 1520 = 5F0
    }
  I2CsendMULTIbyte();
  }

  if (M5.BtnB.wasPressed() && dial == 0 && AM_FM == "FM") {
    REGISTER_ADDRESS = TUNE;
    I2CreadMULTIbyte();

    data = (data - 0x0002) | 0B1000000000000000; // - 100KHz step   = 100/50  76MHz - 94.1MHz
    if ( (data & 0B0000111111111111) <1520) {  //<76MHz
      data = 0B1000011101011010; // 94.1MHz  1882
    }
  I2CsendMULTIbyte();
  }
```
  
[![紹介動画]()](https://youtu.be/vZIJL4G87UQ)

## 関連ブログ
https://massa4649.com/kt0913_1/

以上です。

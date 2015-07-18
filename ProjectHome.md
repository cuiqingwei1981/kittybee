# KittyBee: An easy way to use ZigBee on your Arduino #

![https://lh5.googleusercontent.com/-YIR4G549Z7c/TwEeCUC6ahI/AAAAAAAAAtg/IuvIA95J-BU/w410-h246-k/2011-12-27%2B16.39.25.jpg](https://lh5.googleusercontent.com/-YIR4G549Z7c/TwEeCUC6ahI/AAAAAAAAAtg/IuvIA95J-BU/w410-h246-k/2011-12-27%2B16.39.25.jpg)

KittyBee是基於TI CC2530 ZigBee 模組,所做出的 Arduino shield.
左邊的是內建chip antenna的 4dbm low power版, 右邊的是可以外接天線的 20dbm high power 版本

目的是讓Arduino使用者可以快速的建立一符合 802.15.4的ZigBee無線網路.
可以迅速運用在資料量低,多節點,需要mesh, routing的網路.
例如,無線溫濕度控制, 電源監控, 環境監控等....

KittyBee有高功率(20dBm)跟低功率(4dBm)版本, 兩者的軟體跟出線都相容.

每一片KittyBee都有catcan creative 基於 TI ZigBee Pro所做的 [CatcanCMDfirmware](CatcanCMDfirmware.md) , 讓使用者可以透過UART來對KittyBee 下命令, 以及傳輸資料. 並已規劃基本的ZigBee網路架構,讓使用者不需再花心思在ZigBee stack 上自己建構ZigBee網路.
基本的說明請參照 wiki裏的 [CatcanCMDfirmware](CatcanCMDfirmware.md)

**Wiki裏的 KittyBee 會說明硬體的架構, [CatcanCMDfirmware](CatcanCMDfirmware.md)則會對基本的網路功能做說明**

**以溫度監控為範例, 示範如何使用KittyBee:**

  1. 使用10K NTC來量測溫度, 請參考wiki裏的[KnowingTempWithArduinoAndNTC](KnowingTempWithArduinoAndNTC.md)
  1. 建立一個3節點的基礎ZigBee網路[SetUpSimpleZigBeeNetwork](SetUpSimpleZigBeeNetwork.md)
  1. 網路中資料傳輸的方法 [PassingDataInKittyBee](PassingDataInKittyBee.md)
  1. 把要傳輸的資料做簡單的封包[SimpleDataFrame](SimpleDataFrame.md)
  1. Arduino Sample code 說明[SampleCodeExplain](SampleCodeExplain.md)

**依據以上5份文件內的說明,您就可以做出您的第一個ZigBee 溫度監測網路**

# 其他相關資訊 #
## 距離 ##
常常有人問 high power的KittyBee可以打多遠?
2.4G 是波長很短的頻帶, 相當容易受天候以及遮蔽物的影響, 擺放的位置以及天線的選擇也有重大的關係, 通常18~20dBm的device都有1Km的實力, download裏的這份 [High power可以打多遠](http://code.google.com/p/kittybee/downloads/detail?name=Range_test_1_high_power.pdf&can=2&q=) 有實際的測試結果



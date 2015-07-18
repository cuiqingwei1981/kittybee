# Introduction #

在[溫度監控網路sample code](http://code.google.com/p/kittybee/downloads/detail?name=temp_sensing_demo.rar&can=2&q=) 裏有device端( 溫度sensor)跟接收端的程式

**temp\_sensing\_NSS** 是sensor 端的程式

**Coor\_LCD\_NSS** 是接收端的程式

兩者都會用到 Arduino NewSoftwareSerial的 library 來跟KKittyBee做溝通, 這樣可以保留原來的UART做為debug 跟程式下載用

請到這裏下載 [NewSoftwareSerial](http://arduiniana.org/NewSoftSerial/NewSoftSerial10c.zip), 解開後放到 Arduino 下的libraries 資料夾裏面( 例如在022的版本裏是\arduino-0022\libraries)


**在進行實做前, 請務必先看完前面幾篇基礎:**

  1. 使用10K NTC來量測溫度, 請參考wiki裏的[KnowingTempWithArduinoAndNTC](KnowingTempWithArduinoAndNTC.md)
  1. 建立一個3節點的基礎ZigBee網路[SetUpSimpleZigBeeNetwork](SetUpSimpleZigBeeNetwork.md)
  1. 網路中資料傳輸的方法 [PassingDataInKittyBee](PassingDataInKittyBee.md)
  1. 把要傳輸的資料做簡單的封包[SimpleDataFrame](SimpleDataFrame.md)




# Details #
**溫度量測端**temp\_sensing\_NSS.pde

Device Nr 這裏是在開始的宣告裏寫好的,所以第一個sensor跟第二個sensor要改好再compile 您也可以修改成用dip switch或其他方式來決定
```
int device_Nr=1;
//int device_Nr=2;
```

接下來我們include NewSoftSerial, D2為RX, D3為TX
math.h 是NTC計算溫度時用的

在 setup()裏我們把兩個serial 都宣告成38400,這部分也可以自己更改, 不過myserial() 這個SoftwareSerial 是拿來接KittyBee, KittyBee UART的default值是38400, 如果這裡更改了, 那KittyBee的baudrate也要修改, 建議都維持38400
```
#include <NewSoftSerial.h>
NewSoftSerial mySerial(2, 3);//2 as RX,3 as TX
#include <math.h>

void setup()
{
Serial.begin(38400); 
  mySerial.begin(38400);
}
```
在 **temp\_sensing\_NSS** 裏面, 我們用[KnowingTempWithArduinoAndNTC](KnowingTempWithArduinoAndNTC.md)裏說的方法用10K NTC 量測出現有的溫度, T10\_C 則把浮點的小數(溫度), 乘以10 再取整數, 方便我們以2 bytes來傳輸
```
 T10_C =  int(Thermister(analogRead(0))*10.)  ;
```

接下來用[SimpleDataFrame](SimpleDataFrame.md)裏講述的方法把device Nr跟T10\_C形成簡單的封包

```
form_array();
```

把形成的封包從SoftWareSerial(D3 pin)傳給KittyBee

最後將T10\_C從Arduino的TX(D1 pin)傳出, 方便從電腦上看到溫度是不是有正確

```
for(int i=0;i<12;i++){
  mySerial.print(arrayData[i]);
}
  Serial.println(T10_C); 
```

如果NTC的接法正確, 應該可以從Arduino IDE的 Serial Monitor裏看到攝氏溫度的10倍, 例如: 225 就是22.5度C

https://lh6.googleusercontent.com/-4mGkceUTzZI/TxUrn7iYjVI/AAAAAAAAA-A/W5P1iQKyZ7Y/w209-h287-k/reading%2Btemp.PNG

**溫度感測端如果要多個, 請務必記得Device Nr要跟著修改, 這裡我們做兩個, Device Nr分別是1跟2**



**接收顯示端**Coor\_LCD\_NSS.pde

接收端的程式, 是負責把Coordinator收到的資料, 做查驗, 之後從serial 傳出, 可以直接在Serial Monitor上看

code的部分也包含接LCD,在上面顯示的部分,這部分使用<LiquidCrystal.h>的library, pin 的接法如下:
  * LCD RS pin to digital pin 8
  * LCD Enable pin to digital pin 9
  * LCD D4 pin to digital pin 4
  * LCD D5 pin to digital pin 5
  * LCD D6 pin to digital pin 6
  * LCD D7 pin to digital pin 7

接收端的code, 把從D2 software serial 的Rx接收進來的資料, 依照[SimpleDataFrame](SimpleDataFrame.md)中說明的data validation的方法, 將傳進來的封包做確認, 確認無誤後的有效封包, 再將其中的device Nr 跟溫度資料組合回來
```
//===============form data============================
  if ( validation ==3)  {
    device_Nr =(arrayData[2]<<8)+arrayData[3];
    if (device_Nr == 1){
     T10_C_1 =(arrayData[4]<<8)+arrayData[5];
       T_C_1=T10_C_1/10.;    
      }// end of if deviceNr==1
    if (device_Nr == 2){
     T10_C_2 =(arrayData[4]<<8)+arrayData[5];
       T_C_2=T10_C_2/10.;    
      }// end of if deviceNr==2
    
  
    //value3 =(arrayData[6]<<8)+arrayData[7];
   // value4 =(arrayData[8]<<8)+arrayData[9]; 
    validation=0;
```

我們的範例裏有兩個devices, 所以有兩個溫度跟兩個device Nr要組

下面這一段是把資料輸出的 Serial, 可以用Serial monitor 看到
```
Serial.print( "T_C_1=");
Serial.println( T_C_1);
Serial.print( "T_C_2=");
Serial.println( T_C_2);
```


Show\_Lcd()這個function會把資料傳到16x2的LCD上
```
void show_Lcd(void){


     lcd.home();
     lcd.print("1");
     lcd.setCursor(2, 0);
     lcd.print("        ");
     lcd.setCursor(2, 0);
      lcd.print(T_C_1,1);
      lcd.print("C");

  
     lcd.setCursor(0, 1);
     lcd.print("2");
     lcd.setCursor(2, 1);
     lcd.print("        ");
     lcd.setCursor(2, 1);
      lcd.print(T_C_2,1);
      lcd.print("C"); 
delay(20);


}//end of show lcd
```

請在一塊Arduino上燒錄接收端的程式, 燒好之後我們要依照準備[SetUpSimpleZigBeeNetwork](SetUpSimpleZigBeeNetwork.md) 架設一個簡單的ZigBee網路, 一定要有一個是Coordinator, 另外兩個可以是:

兩個Routers,

或是,兩個End device,

或是,一個 router, 一個End device.

之後我們需要把這3片KittyBee 按照[KittyBee](KittyBee.md) 裏的硬體說明把Power source的 jumper跟UART connector的jumper設好, 像這樣**(三片的接法都一樣)**:

**Power source selector:**

![https://lh5.googleusercontent.com/-jl5wfWuGObw/TwUvH4MHsvI/AAAAAAAAA0U/5U00uzAIffc/w456-h168-k/2012-01-05%2B12.32.54.jpg](https://lh5.googleusercontent.com/-jl5wfWuGObw/TwUvH4MHsvI/AAAAAAAAA0U/5U00uzAIffc/w456-h168-k/2012-01-05%2B12.32.54.jpg)

**UART Connector :**

![https://lh5.googleusercontent.com/-0ZJq-W916P0/TwUvGmYntMI/AAAAAAAAA0Y/QDPgwWIdsJY/w911-h308-k/2012-01-05%2B12.33.11.jpg](https://lh5.googleusercontent.com/-0ZJq-W916P0/TwUvGmYntMI/AAAAAAAAA0Y/QDPgwWIdsJY/w911-h308-k/2012-01-05%2B12.33.11.jpg)

現在我們要組合KittyBee跟燒錄好code的Arduino, 三片的組法都一樣,**Coordinator** **接收顯示端** 跟 **Router** 或 結合 **End device** 跟\*溫度量測端**結合**

**溫度量測端** 端的NTC sensor記得要接回去...

![https://lh4.googleusercontent.com/-PjiWt_z2NtU/TxU6_6yHh1I/AAAAAAAAA-E/LBGLxUUsBNA/w383-h287-k/2012-01-17%2B17.06.36%2B%2528%25E4%25B8%25AD%25E5%259E%258B%2529.jpg](https://lh4.googleusercontent.com/-PjiWt_z2NtU/TxU6_6yHh1I/AAAAAAAAA-E/LBGLxUUsBNA/w383-h287-k/2012-01-17%2B17.06.36%2B%2528%25E4%25B8%25AD%25E5%259E%258B%2529.jpg)


這時把兩個溫度量測端接上電源:

![https://lh4.googleusercontent.com/-XyeSlyfpbcw/TxVDPsw8mPI/AAAAAAAAA-g/FbuJZWY-mZc/w236-h314-k/2012-01-17%2B17.39.30%2B%2528%25E4%25B8%25AD%25E5%259E%258B%2529.jpg](https://lh4.googleusercontent.com/-XyeSlyfpbcw/TxVDPsw8mPI/AAAAAAAAA-g/FbuJZWY-mZc/w236-h314-k/2012-01-17%2B17.39.30%2B%2528%25E4%25B8%25AD%25E5%259E%258B%2529.jpg)

接收端應該會在Serial Monitor輸出溫度:

https://lh3.googleusercontent.com/-VSEo5WIfuhc/TxVDPjcXN-I/AAAAAAAAA-k/h-yl1zp4pVo/w249-h317-k/tempout.PNG

如果您有接16x2的LCD, 應該會是這樣:

![https://lh6.googleusercontent.com/-ko6preINRf0/TxVDPokmuCI/AAAAAAAAA-o/HvuGSmAzD94/w417-h314-k/2012-01-17%2B17.39.41%2B%2528%25E4%25B8%25AD%25E5%259E%258B%2529.jpg](https://lh6.googleusercontent.com/-ko6preINRf0/TxVDPokmuCI/AAAAAAAAA-o/HvuGSmAzD94/w417-h314-k/2012-01-17%2B17.39.41%2B%2528%25E4%25B8%25AD%25E5%259E%258B%2529.jpg)
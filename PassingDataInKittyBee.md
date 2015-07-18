# Introduction #
根據[SetUpSimpleZigBeeNetwork](SetUpSimpleZigBeeNetwork.md)我們已經建立起了基礎網路,接下來我們要說明資料傳輸的方法


# Details #

## 預設的資料傳輸 ##

[CatcanCMDfirmware](CatcanCMDfirmware.md)裏面有提到我們預設的資料傳輸方法:

-End Device(或Router)會把UART上, RX所收到的資料傳回 Coordinator, 從Coordinator的TX原封不動傳出

-Coordinator會把RX所收到的任何資料, broadcast傳到網路裏的每一個device, 從該device的TX傳出

**下面這三個視窗從上到下分別是Coordinator, Router, End device,
ping node的command 最後一個byte可以看出device type.(00=Coor, 01=router, 02=End device)**

https://lh5.googleusercontent.com/-QWWg5yrJ7V4/TwqaPmy_DFI/AAAAAAAAA7A/ub4yX5NZ3F4/w462-h320-k/passdata1.PNG

**這時我們從Router輸入80bytes的資料, 你可以看到, Coordinator就傳出了80bytes.**

https://lh5.googleusercontent.com/-ksPS31QW4aM/Twqbr3iwVxI/AAAAAAAAA74/LS-Fwszlnto/w447-h287-k/passdata2.PNG

**再從End device 輸入80bytes的資料, 資料也是從Coordinator傳出, 不論RF的路線是否經過Router, Router都不會有輸出**

https://lh6.googleusercontent.com/-kuBfgsC9BYE/TwqeXsUoBEI/AAAAAAAAA8o/NbEQGg8xxnw/w372-h269-k/passdata3.PNG


**下圖中我們從Coordinator輸入80bytes的資料, 你可以看到, Router跟End device都輸出了相同的資料**

https://lh5.googleusercontent.com/-aOMpDcPdEOg/Twqbr5w_W3I/AAAAAAAAA8A/7EOVa1KaWDU/w378-h267-k/passdata4.PNG

以上這些是default的傳輸方法, 這樣的結構已經可以做一些簡單的ModBus protocol.

## 指定傳輸 ##

**[Catcan ZigBee API](http://code.google.com/p/kittybee/downloads/detail?name=Catcan%20ZigBee%20API.doc&can=2&q=)裏有提到使用command set來傳遞資料給指定的device. 我們可以根據 device的 Short Address或是IEEE address 來針對特定device 傳輸**

例如現在要從 short address= A0 11的device 對 short address=08 7B 的device傳遞 4個bytes的資料(00 11 22 33)
那應該是要這樣下:

**FE 00 43 07 08 7B 04 00 11 22 33 33**

**FE 00 43** 是command的header跟command本身

**07** 代表後面有7個bytes

**08 7B** 是收件的Short Address

**04** 則是代表 要傳送的資料有 4個bytes

**00 11 22 33** 是這4個bytes的內容

**33** 最後一個byte是 FCS,剛好是33

**08 7B 這個device 會把收到的封包, 轉成從TX這樣輸出:**

**FE 10 43 08 A0 11 04 00 11 22 33 EE**

**FE 1043** 表示是由 FE 00 43的command發出來的

**08** 代表後面有8個bytes.

**A0 11** 是發件者的short address

**04 00 11 22 33** 代表了資料長度跟資料內容

**EE** 是FCS


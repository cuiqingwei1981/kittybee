# Introduction #
This section shows the steps that ZigBee Network is set-up so that the coordinator is jointed by routers in parallel connection.


# Details #
請按照以下步驟操作：
  1. coordinator單獨給電，若coordinator已經連過網路，請reset它。
  1. 將router給電，若router之前曾經連過ZigBee網路，請reset它。
  1. 確定router連上coordinator之後斷電。
  1. 如果還要再接一個router，重複步驟2~3；若已接完，則可透過SA與BroudCast來驗證整個網路是否正常。
註：coordinator要持續給電。
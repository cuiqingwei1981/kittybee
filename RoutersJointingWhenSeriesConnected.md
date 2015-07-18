# Introduction #
This section shows the steps that ZigBee Network is set-up so that the coordinator is jointed by routers in series connection.

# Details #
請按照以下步驟操作：
  1. coordinator單獨給電，若coordinator已經連過網路，請reset它。
  1. router給電，若router之前曾經連過ZigBee網路，請reset它。
  1. 若要在現在這一個router底下再接一個router，請將 **要當child的router** 給電。(註一)
  1. 若 **要當child的router** 之前曾經連過Zigbee網路，請reset **要當child的router**
  1. 如果還要往下接一個router，關掉 **要當parent的router** 並重複步驟3~5；若已接完，則可透過[SA](ShortAddress.md)與BroadCast來驗證整個網路是否正常。
註一：此時coordinator與 **要當parent的router** 都要給電，請將coordinator放置在較遠的地方或者在coordinator周圍加上屏蔽物(譬如鐵蓋)
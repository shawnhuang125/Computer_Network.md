# TCP/IP運作原理簡述
- TCP/IP協議是OSI模型的簡化版,是一種網路通訊協議的架構。
## Network Interface Layer | OSI Physical Layer and Data Link Layer
- Usage Overview:
- 包含光纖電纜設備與路由器的供電設備,通過Vlan網域隔離技術到達目標網段,通過MAC地址進行設備間的通信
## Internet Layer |  OSI Network Layer
- Usage Overview:
- 給數據包附上路由地址與公共IP,NAT等訊息。路由協議都在這一層。
## Transport Layer |  OSI Transport Layer
- 將加密後的傳輸數據附上序號並排序,切割成封包,或者將封包解封。
## Application Layer | OSI Application Layer and Presentation Layer and Session Layer
- 網頁訪問,DNS網域解析,數據加密,通訊對話連接

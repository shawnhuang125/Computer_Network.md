# TCP/IP運作原理簡述
- TCP/IP協議是OSI模型的簡化版,是一種網路通訊協議的架構。
## Network Interface Layer | OSI Physical Layer and Data Link Layer
- Usage Overview:
- 包含光纖電纜設備與路由器的供電設備,通過Vlan網域隔離技術到達目標網段,通過MAC地址進行設備間的通信
## Internet Layer |  OSI Network Layer
- Usage Overview:
- 給數據包附上路由地址與公共IP,NAT等訊息。路由協議都在這一層。
## Transport Layer |  OSI Transport Layer
|通訊協議|TCP|UDP|
|:-------:|:-------:|:-------:|
| 性質相比 | 數據處理較複雜,可靠性高,安全性高|傳輸速度較TCP快,可靠性與安全性較TCP差 | 
| 主要功能 | 文件下載,信件寄送,網頁瀏覽 | 語音通話,視訊通話 |
| 協議 | HTTP(HyperText Transport Portocol)/HTTPS(HyperText Transport Portocol Secure),FTP(Flies Transport Portocol),SMTP(Simple Mail Transport Portocol),IMAP(Internet Mail Access Portocol),SSH(Secure Shell) | RTP(Real-Time Transport Portocol),RTCP(Real-Time Transport Control Portocol)|
## Application Layer | OSI Application Layer and Presentation Layer and Session Layer
- 網頁訪問,DNS網域解析,數據加密,通訊對話連接

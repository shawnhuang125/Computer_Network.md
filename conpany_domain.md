# 典型公司內網架構的設計參考
## 網域結構解析
1. 核心交換器（Core Switch）：

- 將所有辦公室的有線和無線設備通過核心交換器進行集中管理。
- 核心交換器支援 VLAN，用來區隔不同的辦公室和無線網段。
- 支援 PoE 的交換器可以同時給無線接入點（AP）供電。
2. Windows Server (Domain Controller/DNS/DHCP/RADIUS) :

- 域控制器（AD DS）：所有辦公室的設備都通過有線或無線連接，並且由域控制器負責身份驗證（登入網域帳戶）。
- DNS：內部網絡的所有名稱解析服務由 Windows Server 的 DNS 服務管理。
- DHCP：Windows Server 負責為內網中的所有設備（有線和無線）分配 IP 地址，確保正確的網絡配置。
- RADIUS：如果需要使用域帳號進行 Wi-Fi 登錄，可以使用 Windows Server 的 RADIUS 功能進行無線身份驗證。
3. VLAN segmentation :

- 將每個辦公室劃分為不同的 VLAN，進行網絡隔離，確保不同辦公室之間的網絡流量互不干擾。
- 為 Wi-Fi 設置一個單獨的 VLAN，確保無線流量不會影響有線網絡的性能，同時便於管理。
4.無線接入點（Access Point, AP）：

- 部署在每個辦公室，確保 Wi-Fi 覆蓋整個辦公區域。所有 AP 都連接到核心交換器，並通過 VLAN 進行流量隔離和管理。
- 所有的無線接入點使用統一的 SSID（網絡名稱），確保無論用戶在公司內的哪個辦公室，都能無縫切換連接到 Wi-Fi。
5. Router :

- 路由器連接到公司外部的網絡（如互聯網），並且處理所有內網與外部網絡之間的流量。
- 路由器將外部的 DNS 查詢轉發到公共 DNS 伺服器（如 Google DNS），讓內網設備能夠訪問外部網站。
6. PC 和其他終端設備：

- 每個辦公室的 PC 會通過網線直接連接到交換器，加入公司網域，並通過 Windows Server 進行身份驗證和資源訪問。
- 使用 Wi-Fi 的設備（如筆記型電腦、手機、平板等）則通過無線接入點連接到內網，並依然通過 Windows Server 進行身份驗證。
## 網域架構圖
```
[Internet] 
   | 
[Router] ----> [Core Switch] -----> [Office 1 VLAN] --> [PCs/Devices]
                      |               [Wi-Fi AP]
                      |
                   [Windows Server]
                      |-----> [Office 2 VLAN] --> [PCs/Devices]
                      |               [Wi-Fi AP]
                      |
                      |-----> [Office 3 VLAN] --> [PCs/Devices]
                                      [Wi-Fi AP]

```
## 注意事項
- 在這樣的架構下，Windows Server AD DS 可能會出現 DNS 問題,因為Winodws Server 將作為內部的DNS伺服器,所以需要手動調整Router及Winodws AD DS的DNS配置
1. 讓 Windows Server 成為唯一的 DNS 伺服器
- 將 Windows Server 設定為內網唯一的 DNS 伺服器。讓所有內部設備都使用 Windows Server 的 DNS 來解析內部和外部網域名稱。
- 內部設備的 DNS 設置應指向 Windows Server，確保所有內部的 DNS 查詢都經由 Windows Server 處理。
2. 配置 DNS 轉發（DNS Forwarding）
- 在 Windows Server 的 DNS 伺服器中，設置 DNS 轉發（DNS Forwarding） 功能。這樣，當 Windows Server 無法解析外部網站（如 google.com）時，會將這些查詢轉發給外部的公共 DNS 伺服器（如 Google 的 8.8.8.8 或 Cloudflare 的 1.1.1.1）。
- 具體步驟如下：
- 打開 DNS 管理控制台。
- 右鍵點擊你的 DNS 伺服器，選擇 屬性。
- 在 轉發器（Forwarders） 標籤頁，新增外部的 DNS 伺服器（如 8.8.8.8）。
- 儲存配置。
- 這樣的配置可以讓 Windows Server 處理內部網域的解析，並將外部域名的解析請求轉發給公共 DNS 伺服器，避免內外網的 DNS 衝突。

3. 禁用路由器的 DNS 服務
- 如果路由器已經啟用了 DHCP 和 DNS 服務，請將路由器的 DNS 功能關閉，確保路由器僅負責轉發流量，而不參與內部的 DNS 解析。
- 這樣可以確保內部設備只使用 Windows Server 的 DNS 伺服器來解析網域，避免雙重 DNS 設置帶來的衝突。
## 可以優化網域架構
```
[Internet] 
   |
[Router] -----> [Core Switch] -----> [Office 1 VLAN] --> [PCs/Devices]
                      |               [Wi-Fi AP]
                      |
                 [Windows Server] (AD DS / DNS / DHCP / RADIUS)
                      |-----> [Office 2 VLAN] --> [PCs/Devices]
                      |               [Wi-Fi AP]
                      |
                      |-----> [Office 3 VLAN] --> [PCs/Devices]
                                      [Wi-Fi AP]
```
1. Router :
- 連接到外部網絡（如互聯網），處理內網設備和互聯網之間的流量。
- 禁用路由器的 DNS 功能，確保內部網絡的 DNS 查詢僅由 Windows Server 處理。
- 路由器的 DHCP 功能可以關閉，因為 Windows Server 會負責 DHCP。

2. 核心交換器（Core Switch）：
- 連接所有有線和無線設備，並劃分 VLAN 進行網絡隔離。
- 所有無線接入點（AP）和有線設備都通過交換器與網域控制器（Windows Server）通信。

3. Windows Server（AD DS / DNS / DHCP / RADIUS）：
- Active Directory Domain Services (AD DS)：管理網域中的身份驗證、用戶管理、電腦帳戶、組策略等。
- DNS 伺服器：Windows Server 將作為內部唯一的 DNS 伺服器，負責解析內部的網域名（如 company.local）和將外部的網域名解析請求轉發給公共 DNS 伺服器。
- DHCP 伺服器：Windows Server 將分配 IP 地址給內部網絡中的所有設備，包括有線和無線設備，確保每個設備都有正確的網絡配置。
- RADIUS 伺服器：可選，負責 Wi-Fi 的身份驗證，確保所有連接到無線網絡的設備都經過域帳戶登錄驗證。
## 防火牆配置圖
1. 邊界防火牆（Edge Firewall）：位於路由器與內網之間
- 位置：防火牆應該部署在路由器和核心交換器之間，位於內部網絡和外部網絡（互聯網）之間的邊界處。
- effect :
- 保護整個內網不受外部威脅（如 DDoS 攻擊、入侵等）。
- 管理所有進出內網的流量，根據企業策略對外部訪問進行篩選或阻擋。
- 設置 NAT（網絡地址轉換）來將外部流量路由到內網的具體伺服器或設備。
- 控制哪些內部設備可以訪問外部互聯網。
2. 內部分區防火牆（Internal Firewall）：用於內部網段之間
- 位置：防火牆可以設置在不同的 VLAN 或部門之間，來管理內部網絡流量。
- effect :
- 控制不同部門或辦公室之間的網絡流量，例如只允許某些 VLAN 之間的數據交互，防止不必要的內部流量。
- 保護敏感的內部網段，例如保護伺服器 VLAN，確保只有特定的用戶或設備能夠訪問伺服器。
```
[Internet]
    |
[Router] ----> [Edge Firewall] ----> [Core Switch]
                    |                  |----> [VLAN 1 - Office 1]
                    |                  |
                    |                  |----> [VLAN 2 - Office 2]
              [Internal Firewall]      |
                    |                  |----> [VLAN 3 - Server VLAN]
```
## 多層VLAN交換器
### Vlan交換器配置簡略說明
- 正常來說,如果配置正確,主交換器可以通過VLAN Tagging與下級交換器之間傳遞 VLAN 流量，並且可以通過管理工具查看哪些設備連接到哪些 VLAN。
- 前提是Vlan ID需要手動配置成一樣的,要看廠牌支持的Vlan協定,這裡來說...大部分的都是依樣的,允許不同場台之間的搭配。
### 多層 VLAN 交換器架構解析：
1. Core Switch：
- 主交換器通常位於網絡的核心部分，連接所有子交換器（Access Switches），並且可能處理跨 VLAN 的路由（如果主交換器是三層交換器）。
- 主交換器的主要任務是通過 Trunk 端口 與下級交換器進行連接，並傳遞所有 VLAN 的流量。
2. Access Switches：
- 下級交換器負責連接具體的設備（如 PC、伺服器、Wi-Fi AP 等），並根據其端口配置將設備分配到相應的 VLAN 中。
- 這些交換器通常也支持 VLAN，並且可以通過 Access 端口 將設備加入特定的 VLAN。
### 多層交換器互相通訊的協議
- 交換器之間兼容的關鍵在於它們是否支持標準的網絡協議，主要是以下幾個協議：
1. IEEE 802.1Q（VLAN 標籤協議）：
- 802.1Q 是 VLAN 的標準協議，用於在交換器之間標記和傳遞 VLAN 流量。
- 只要交換器支持 802.1Q，不同廠牌的交換器也能夠通過 Trunk 端口 進行 VLAN 流量的傳遞。

2. Trunk port 配置 :
- 主交換器和副交換器之間的連接通常是配置為 Trunk 端口，用於傳遞多個 VLAN 的流量。只要這些交換器支持標準的 Trunk 配置，並且雙方的 VLAN ID 和 Tagging 是一致的，就可以實現不同廠牌交換器之間的互通。

3.RSTP / MSTP（生成樹協議）：
- 如果你的網絡中有冗餘鏈路，則需要使用 生成樹協議（Spanning Tree Protocol, STP） 來防止環路問題。大多數廠牌的交換器支持標準的 RSTP（快速生成樹協議）或 MSTP（多實例生成樹協議），這樣就可以跨不同廠牌之間協同工作。
#### 環路問題的原因
- 冗餘連接：為了提高網路的可靠性，常常會在網路中添加冗餘路徑。但如果沒有適當的環路預防機制，這些冗餘路徑可能形成環路。
- 配置錯誤：網路設備配置不當，或者錯誤連接設備，可能導致環路的產生。
- 設備故障：交換機或橋接器的故障可能導致其無法正確處理數據包，形成環路。
#### 環路問題的影響
- 廣播風暴：數據包在環路中不斷循環，造成大量廣播，佔用網路帶寬。
- MAC地址表混亂：交換機的MAC地址表不斷更新錯誤的端口信息，導致數據無法正確轉發。
- 網路延遲和丟包：環路會增加網路延遲，甚至導致數據包丟失。
- 設備過載：大量無用的流量會使網路設備過載，影響正常服務。
#### 環路問題的解決方法
- 生成樹協議（STP）：啟用STP，可以自動檢測網路中的環路，並阻塞冗餘路徑，形成一棵無環的樹形結構。
- 快速生成樹協議（RSTP）：RSTP是STP的改進版本，能更快地響應拓撲變化。
- 配置環路保護：在交換機上啟用環路檢測和保護功能，及時發現並阻斷環路。
- 正確設計網路拓撲：在網路設計階段，就應避免不必要的環路結構。
- 定期檢查和維護：定期檢查網路設備和連接狀態，確保配置正確。
4. LLDP（鏈路層發現協議）：
- LLDP 是一種標準的設備發現協議，可以跨不同廠牌的網絡設備運行。這樣，即使你的主交換器和副交換器來自不同廠牌，它們仍然可以發現並標記對方，方便管理。

# zerotier-planet
https://github.com/xubiaolin/docker-zerotier-planet.git
1：ZeroTier介紹
ZeroTier是一款強大的P2P VPN工具，它可以讓你在網路上建立屬於自己的虛擬區域網路。透過它，你可以輕鬆實現遠端存取家中設備的需求——例如在公司用手機直接存取家中的NAS。最重要的是，設備之間是點對點直連的，經過中轉伺服器，既保證了速度，又提升了安全性。

它的工作原理是這樣的：透過ZeroTier One客戶端，在不同的設備（如筆記本、手機、伺服器等）之間建立P2P連接，即使這些設備都在NAT後面也沒有問題。它採用了STUN等技術，可以改變大多數類型的NAT，實現設備間的直接通訊。如果確實無法直連，將會透過中轉伺服器進行通訊。

簡單來說，ZeroTier就像是一個跨越互聯網的“虛擬交換機”，讓分佈在世界各地的設備上，就像在同一個交換機內部一樣方便地相互訪問。

零層

ZeroTier網路中的關鍵概念

PLANET（行星伺服器）：ZeroTier網路的核心根伺服器，負責網路發現和最終連線。相當於整個網路的「中樞」。

MOON（衛星伺服器）：使用者可以自建的樹根伺服器。它可以作為區域性的代理節點，幫助就近的設備更快建立連接，提升網路效能。

LEAF（葉子節點）：所有連接 ZeroTier 網路的終端設備，如電腦、手機、伺服器等。這些設備透過 PLANET 和 MOON 的協調來相互發現和通訊。

本教學將引導您建立筆記本的 PLANET 伺服器，讓您完全掌控自己的 ZeroTier 網路。

2：為什麼要自建PLANET伺服器
自建PLANET伺服器有以下幾個重要原因：

提升網路穩定性：官方伺服器位於海外，國內用戶存取延遲高且不穩定。自建伺服器可以大幅提升連線品質。

加快連線速度：在地化的PLANET伺服器可以更快地幫助裝置建立P2P連線。

增強您的網路控制：自建伺服器讓網路配置完全掌控，可以根據需求進行最佳化調整。

提高安全性：中部化部署意味著網路流量不經過第三方伺服器，更加安全可靠。

降低依賴：避免因官方伺服器故障或網路維護而影響您的業務正常運作。

3：開始安裝
3.1：環境準備
在開始安裝之前，請確保您的伺服器符合以下條件：

服務要求:

擁有公網IP位址
需開放以下連接埠:
3443/tcp（管理面板，依實際情況調整）
9994/tcp（ZeroTier通信，依實際情況調整）
9994/udp（ZeroTier通信，依實際情況調整）
軟體依賴：

Docker（容器運行環境）
Git (取得項目代碼)
系統需求：

推薦使用較新的Linux發行版本：
Debian 12
Ubuntu 20.04+
Rocky Linux
其他同類系統
3.1.1 安裝git
#debian/ubuntu等
apt update && apt install git -y 

#centos等
yum update && yum install git -y 
3.1.2 安裝docker
curl -fsSL https://get.docker.com |bash 
若網路問題，導致無法安裝，可使用國內鏡像安裝：請參考：安裝Docker

3.1.3 啟動docker
service docker start
3.1.4 配置docker加速鏡像（可選，不配也可以）
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": [
        "https://docker.mirrors.aster.edu.pl",
        "https://docker.mirrors.imoyuapp.win"
    ]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
3.2：下載專案原始碼
官方地址

git clone https://github.com/xubiaolin/docker-zerotier-planet.git
加速位址

git clone https://ghproxy.imoyuapp.win/https://github.com/xubiaolin/docker-zerotier-planet.git
3.3：執行安裝腳本
進入專案目錄

cd docker-zerotier-planet
運行deploy.sh腳本

./deploy.sh
根據提示來選擇即可，完成操作後會自動安裝

欢迎使用zerotier-planet脚本，请选择需要执行的操作：
1. 安装
2. 卸载
3. 更新
4. 查看信息
5. 退出
请输入数字：
整個劇本預計 1-3 分鐘，具體需要看網路與週邊設備

當您看到類似如下字樣時，表示安裝成功

安裝完成

3.4 下載planet文件
腳本運行完成後，會在./data/zerotier/dist目錄下產生planet和moon設定檔。

您可以透過以下兩種方式取得這些文件：

安裝完成後提供的URL直接下載
使用scp或其他檔案傳輸工具從伺服器下載
請儲存這些文件，後續配置客戶端時會用到。

3.5 新建網絡
存取http://ip:3443控制器頁面

使用者介面

使用預設帳號為：admin

預設密碼為:password

3.5.1 創建網絡
登入後點選「網路」選單，然後點選「新增網路」按鈕建立新網路。

在建立網路頁面中，輸入一個識別的網路名稱，其他選項可保持預設。點選「建立網路」按鈕完成建立。

建立成功後系統會自動產生一個網路ID，該ID會在後續客戶端設定時使用，請記錄下來。

使用者介面

得到網絡id

使用者介面

3.5.2 分配網路IP:
確認輕鬆設定 分配ID

產生ip範圍 ip位址

4.客戶端配置
ZeroTier支援多種主流網路的用戶端，包括：

視窗
macOS
Linux
安卓
4.1 Windows 配置
首先去zerotier官網下載zerotier客戶端

將planet檔案覆蓋貼到C:\ProgramData\ZeroTier\One中(這個目錄是目錄，需要運允許查看隱藏目錄才行)

Win+S 搜尋服务

使用者介面

找到ZeroTier One，並且重啟服務

使用者介面

4.2 加入網絡
使用管理員身分開啟PowerShell

執行以下指令，看到join ok字樣就成功了

PS C:\Windows\system32> zerotier-cli.bat join 网络id(就是在网页里面创建的那个网络)
200 join OK
PS C:\Windows\system32>
登入管理後台可以看到有一個新的客戶端，勾選Authorized就行

使用者介面

IP分配裡面會出現zerotier的內部網路ip

ip

執行如下命令：

PS C:\Windows\system32> zerotier-cli.bat peers
200 peers
<ztaddr>   <ver>  <role> <lat> <link> <lastTX> <lastRX> <path>
fcbaeb9b6c 1.8.7  PLANET    52 DIRECT 16       8994     1.1.1.1/9993
fe92971aad 1.8.7  LEAF      14 DIRECT -1       4150     2.2.2.2/9993
PS C:\Windows\system32>
可以看到有一個PLANTET和LEAF角色，連接方式直接DIRECT(直連)

到這裡就加入網路成功了

4.2 Linux客戶端
步驟如下：

安裝linux客戶端軟體
進入目錄/var/lib/zerotier-one
替換目錄中的planet文件
重啟zerotier-one服務( service zerotier-one restart)
加入網路zerotier-cli join網絡id
管理後台同意加入請求
zerotier-cli peers可見 planet角色
4.3 安卓客戶端配置
Zerotier非官方安卓用戶端

4.4 MacOS客戶端配置
步驟如下：

進入/Library/Application\ Support/ZeroTier/One/目錄，並替換目錄下的planet文件
重新啟動ZeroTier-One：cat /Library/Application\ Support/ZeroTier/One/zerotier-one.pid | sudo xargs kill
加入網路zerotier-cli join網絡id
管理後台同意加入請求
zerotier-cli peers可見 planet角色
4.5 OpenWRT客戶端配置
步驟如下：

安裝zerotier客戶端
進入目錄/etc/config/zero/planet
替換目錄中的planet文件
在openwrt網頁後台先關閉zerotier服務，在開啟zerotier服務
在openwrt網頁後台加入網絡
管理後台同意加入請求
執行ln -s /etc/config/zero /var/lib/zerotier-one 
zerotier-cli peers可見 planet角色
4.6 iOS客戶端配置
方案一：越獄後安裝ZeroTie，然後替換planet文件

方案二：使用Wireguard接上ZeroTier網絡

5. 管理面板SSL配置
管理面板的SSL支援需自行配置，參考Nginx配置如下：

upstream zerotier {
  server 127.0.0.1:3443;
}

server {

  listen 443 ssl;

  server_name {CUSTOME_DOMAIN}; #替换自己的域名

  # ssl证书地址
  ssl_certificate    pem和或者crt文件的路径;
  ssl_certificate_key key文件的路径;

  # ssl验证相关配置
  ssl_session_timeout  5m;    #缓存有效期
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;    #加密算法
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    #安全链接可选的加密协议
  ssl_prefer_server_ciphers on;   #使用服务器端的首选算法


  location / {
    proxy_pass http://zerotier;
    proxy_set_header HOST $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

server {
    listen       80;
    server_name  {CUSTOME_DOMAIN}; #替换自己的域名
    return 301 https://$server_name$request_uri;
}
6. 卸載
docker rm -f zerotier-planet
7：問答：
1.為什麼我ping不通目標機器？
請檢查防火牆設置，Windows系統需要允許ICMP入站，Linux同理

2. IOS客戶端怎麼用？
iOS 用戶端外掛在這裡，裝置需要越獄：https://github.com/lemon4ex/ZeroTieriOSFix

3.為什麼看不到官方的星球
專案剔除官方伺服器，只保留了自訂的星球節點

4.我更換了IP要怎麼處理？
如果IP更換了，則需要重新部署，相當於全新部署

5. PVE lxc容器沒有配置頻寬
需要修改lxcContainer的配置，同時lxcContainer需要取消勾選无特权

設定檔在/etc/pve/lxc/{ID}.conf

在Proxmox7.0之前的版本中加入以下內容：

lxc.cgroup.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
在Proxmox7.0之後的版本加入以下內容：

lxc.cgroup2.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
6.管理後台忘記密碼怎麼辦：
執行./deploy.sh，選擇重設密碼即可

7. 為什麼連不上星球
請檢查防火牆，如果是阿里雲、騰訊雲端用戶，需要在對應平台後台防火牆放行埠。 linux機器上也要放行，如果安裝了ufw等防火牆工具。

8.如何判斷是直連還是中轉
管理員權限執行終端，運行zerotier-cli peers

<ztaddr>   <ver>  <role> <lat> <link>   <lastTX> <lastRX> <path>
69c0d507d0 -      LEAF      -1 RELAY
93caa675b0 1.12.2 PLANET  -894 DIRECT   4142     4068     110.42.99.46/9994
ab403e2074 1.10.2 LEAF      -1 RELAY
如果你的ztaddr是REPLAY，就表示是中轉

9.為什麼我的零級傳輸不穩定
由於zerotier使用的是udp協議，因此部分地區可能對udp進行了qos，可以考慮使用openvpn。

10.支持旅程嗎？
暫且

11. ARM伺服器可以搭建嗎？
可以

12.支援docker-compose啟動配置嗎
參考docker-compose檔案如下

version: '3'

services:
  myztplanet:
    image: xubiaolin/zerotier-planet:latest
    container_name: ztplanet
    ports:
      - 9994:9994
      - 9994:9994/udp
      - 3443:3443
      - 3000:3000
    environment:
      - IP_ADDR4=[IPV4IP ADDRESS]
      - IP_ADDR6=
      - ZT_PORT=9994
      - API_PORT=3443
      - FILE_SERVER_PORT=3000
    volumes:
      - ./data/zerotier/dist:/app/dist
      - ./data/zerotier/ztncui:/app/ztncui
      - ./data/zerotier/one:/var/lib/zerotier-one
      - ./data/zerotier/config:/app/config
    restart: unless-stopped

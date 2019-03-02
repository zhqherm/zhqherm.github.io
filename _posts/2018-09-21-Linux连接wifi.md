# Linux 连接wifi

## 查看日志中出现的与network相关的信息

- cat /var/log/messages \| grep network

## 安装步骤

### 1. dmesg \| grep firmware（非必要步骤）

- 查看是否有来自无线网卡的固件请求

### 2. iw dev（非必要步骤）

- 查看无线网口，interface后面即为无线网口号

    ``` shell
    phy#0
    Interface wlp2s0
        ifindex 3
        wdev 0x1
        addr a4:db:30:84:4b:1c
        type managed
    ```

- 如果连接成功会多出下面的两行，显示 SSID 和信道

    ``` shell
    ssid CMCC
    channel 11 (2462 MHz), width: 40 MHz, center1: 2452 MHz
    ```

### 3. ip link set wlp3s0 up（必要）

- 激活无线网络接口

### 4. ip link show wlp3s0（非必要步骤）

- 检验接口是否激活成功

    ``` shell
    wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state DOWN mode DORMANT group default qlen 1000
    link/ether 00:11:22:33:44:55 brd ff:ff:ff:ff:ff:ff 
    ```

- <BROADCAST,MULTICAST,UP,LOWER_UP> 中的UP 表明该接口激活成功，后面的 state DOWN 无关紧要。

### 5. iw wlp3s0 link（非必要步骤）

- 查看无线网络连接情况

### 6. iw wlp3s0 scan \| grep SSID（如果知道 Wifi 名字，就不需要）

- 扫描可连接的wifi

### 7. wpa_supplicant -B -i wlp3s0 -c < (wpa_passphrase "ssid" "psk")（必要）

- 连接指定的SSID，将ssid 替换为实际的网络名称，psk 替换为无线密码，请保留引号。
- Successfully initialized wpa_supplicant //  连接成功标志

### 8. dhclient wlp3s0（必要）

- 用dhcp 获得 IP 分配

### 9. ip addr show wlp3s0（必要）

- 查看是否成功地通过dhcp自动获取了ip地址，如果分配有ip，即可上网，也可以有ping直接测试

    ``` shell
    wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether a4:db:30:84:4b:1c brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.101/24 brd 192.168.1.255 scope global dynamic wlp3s0
    valid_lft 7195sec preferred_lft 7195sec
    inet6 fe80::a6db:30ff:fe84:4b1c/64 scope link 
    valid_lft forever preferred_lft forever
    ```

### 10. yum install net-tools
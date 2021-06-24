# kali Linux安装网件A7000驱动

## 包/构建依赖关系（卡利）

```shell
$ sudo apt-get update && apt-get upgrade
$ sudo apt-get install build-essential libelf-dev linux-headers-`uname -r`
```
## 下载

### 克隆驱动包到本地

```shell
git clone https://github.com/aircrack-ng/rtl8812au.git
```

### 下载patch文件

```shell
https://github-repository-files.githubusercontent.com/101095696/5862527?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20210624%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20210624T105027Z&X-Amz-Expires=300&X-Amz-Signature=59f93801a96624759a5e83f9b93dc29d4a38db2d9adf63e075f2d3334dc3383e&X-Amz-SignedHeaders=host&actor_id=85304402&key_id=0&repo_id=101095696&response-content-disposition=attachment%3Bfilename%3D0001-Allow-inject-management-and-control-frames.txt&response-content-type=text%2Fplain
```

将下载的patch文件移动到rtl8812au文件包中

```shell
0001-Allow-inject-management-and-control-frames.txt
```

### 在执行下面命令：

```shell
sudo patch -p1 < 0001-Allow-inject-management-and-control-frames.txt
```

## 编辑部分

### 编辑Makefile文件

```shell
edit Makefile
```

### 编辑内容如下：

```shell
Change line 26 from "CONFIG_RTL8814A = n" to "CONFIG_RTL8814A = y"
Change line 181 from "ifeq ($(CONFIG_RTL8812A)$(CONFIG_RTL8821A)$(CONFIG_RTL8814A), y_y_n)" to "ifeq ($(CONFIG_RTL8812A)$(CONFIG_RTL8821A)$(CONFIG_RTL8814A), y_y_y)"
Change line 460 from "#RTL871X = rtl8814a" to "RTL871X = rtl8814a"
```

## 编译与安装部分

### 编译Makefile文件

```shell
sudo make
```

### 驱动编译好了后安装：

```shell
sudo make install
```

## 调试部分

### 重启

```shell
reboot
```

### 调试

```shell
enable monitor mode :
```

#### 设置界面

```shell
sudo ip link set wlan0 down
```

#### 设置监视器模式

```shell
 sudo iw dev wlan0 set type monitor 
```

#### 设置界面

```shell
sudo ifconfig wlan0 up 
```

#### 设置TX电源

```shell
sudo iw wlan0 set txpower fixed 3000
```

```shell
sudo rfkill unblock wifi
```

### USB 模式开关

0：不切换，1：从usb2.0切换到usb 3.0 2：从usb3.0切换到usb 2.0

```shell
$ rmmod 88XXau
$ modprobe 88XXau rtw_switch_usb_mode=int (0: no switch 1: switch from usb2 to usb3 2: switch from usb3 to usb2)
```

### 重启

```shell
reboot
```

### 网络管理器

网络管理器的新版本切换到随机 MAC 地址。有些用户更喜欢使用固定地址。只需在下面添加这些行

```shell
[device]
wifi.scan-rand-mac-address=no
```

在文件/等/网络管理/网络管理.conf结束时，使用命令重新启动网络管理器：

```shell
$ sudo service NetworkManager restart
```

### LED 控制

#### 静态按模块参数在 /等 / 模组配置. d/ 8812au. conf 或任何地方， 例如：

```shell
options 88XXau rtw_led_ctrl=0
```

值可以是0或1

#### 或动态地写信给 /proc/net/rtl8812au/$（您的界面名称）/led_ctrl，例如：

```shell
$ echo "0" > /proc/net/rtl8812au/$(your interface name)/led_ctrl
```

值可以是0或1

#### 检查当前值：

```shell
$ cat /proc/net/rtl8812au/$(your interface name)/led_ctrl
```

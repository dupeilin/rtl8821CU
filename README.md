# Realtek RTL8811CU/RTL8821CU USB wifi adapter driver version 5.4.1 for Linux 4.4.x up to 5.x

Before build this driver make sure `make`, `gcc`, `linux-header` and `git` have been installed.

## 首先下载仓库
```
mkdir -p ~/build
cd ~/build
git clone https://github.com/brektrou/rtl8821CU.git
```
## Build and install with DKMS 使用 DKMS 构建和安装 我没有用 我用下面的make 和install

DKMS is a system which will automatically recompile and install a kernel module when a new kernel gets installed or updated. To make use of DKMS, install the dkms package.

### Debian/Ubuntu:
```
sudo apt-get install dkms
```
### Arch Linux/Manjaro:
```
sudo pacman -S dkms
```
To make use of the **DKMS** feature with this project, just run:
```
./dkms-install.sh
```
If you later on want to remove it, run:
```
./dkms-remove.sh
```

### Plug your USB-wifi-adapter into your PC
插入您的usb -wi - fi适配器到您的PC
If wifi can be detected, congratulations.
如果能探测到wifi，恭喜你。
If not, maybe you need to switch your device usb mode by the following steps in terminal:
如果没有，您可能需要切换您的设备usb模式，以下步骤在终端:
1. find your usb-wifi-adapter device ID, like "0bda:1a2b", by type:
1. 找到您的usb-wi - fi适配器设备ID，如“0bda:1a2b”，按类型:
```
lsusb
```
2. switch the mode by type: (the device ID must be yours.)
2. 按类型切换模式:(设备ID必须是你的)

Need install `usb_modeswitch` (Archlinux: `sudo pacman -S usb_modeswitch`)
需要安装' usb_modeswitch ' (Archlinux: ' sudo pacman -S usb_modeswitch ')
```
sudo usb_modeswitch -KW -v 0bda -p 1a2b
systemctl start bluetooth.service - starting Bluetooth service if it's in inactive state
```

It should work.
它应该工作。
### Make it permanent
   永久工作

If steps above worked fine and in order to avoid periodically having to make `usb_modeswitch` you can make it permanent (Working in **Ubuntu 18.04 LTS**):
如果以上步骤工作良好，为了避免周期性地使' usb_modeswitch '，你可以使它永久(工作在**Ubuntu 18.04 LTS**):
1. Edit `usb_modeswitch` rules:
 编辑“usb_modeswitch”规则:

   ```bash
   sudo nano /lib/udev/rules.d/40-usb_modeswitch.rules
   ```

2. Append before the end line `LABEL="modeswitch_rules_end"` the following:
  在结束行' LABEL="modeswitch_rules_end" '之前追加以下内容:
   ```
   # Realtek 8211CU Wifi AC USB
   ATTR{idVendor}=="0bda", ATTR{idProduct}=="1a2b", RUN+="/usr/sbin/usb_modeswitch -K -v 0bda -p 1a2b"
   ```   

## Build and install without DKMS   
不用DKMS 构建  我直接在这里make 和install 的  #sudo modprobe 8821cu  不知道什么意思 我没有运行也成功了  到这里就可以结束了  如果插入没反应你在往下看
Use following commands:
```
cd ~/build/rtl8821CU
make
sudo make install
#sudo modprobe 8821cu  不知道什么意思 我没有运行也成功了
```
If you later on want to remove it, do the following:
你可以这样卸载驱动 
```
cd ~/build/rtl8821CU
sudo make uninstall
```
## Checking installed driver
If you successfully install the driver, the driver is installed on `/lib/modules/<linux version>/kernel/drivers/net/wireless/realtek/rtl8821cu`. Check the driver with the `ls` command:
```
ls /lib/modules/$(uname -r)/kernel/drivers/net/wireless/realtek/rtl8821cu
```
Make sure `8821cu.ko` file present on that directory

### Check with **DKMS** (if installing via **DKMS**):

``
sudo dkms status
``
### ARM architecture tweak for this driver (this solves compilation problem of this driver):
此驱动程序的ARM架构调整(解决此驱动程序的编译问题):
```
# For AArch32
sudo cp /lib/modules/$(uname -r)/build/arch/arm/Makefile /lib/modules/$(uname -r)/build/arch/arm/Makefile.$(date +%Y%m%d%H%M)
sudo sed -i 's/-msoft-float//' /lib/modules/$(uname -r)/build/arch/arm/Makefile
sudo ln -s /lib/modules/$(uname -r)/build/arch/arm /lib/modules/$(uname -r)/build/arch/armv7l

# For AArch64
sudo cp /lib/modules/$(uname -r)/build/arch/arm64/Makefile /lib/modules/$(uname -r)/build/arch/arm64/Makefile.$(date +%Y%m%d%H%M)
sudo sed -i 's/-mgeneral-regs-only//' /lib/modules/$(uname -r)/build/arch/arm64/Makefile

```
### Monitor mode
监控模式
Use the tool 'iw', please don't use other tools like 'airmon-ng'
```
iw dev wlan0 set monitor none
```
### Raspberry Pi 树莓派
To build this driver on Raspberry Pi you need to set correct platform in Makefile. Change
```
CONFIG_PLATFORM_I386_PC = y
CONFIG_PLATFORM_ARM_RPI = n
CONFIG_PLATFORM_ARM_RPI3 = n
```
to
```
CONFIG_PLATFORM_I386_PC = n
CONFIG_PLATFORM_ARM_RPI = y
CONFIG_PLATFORM_ARM_RPI3 = n
```
For the Raspberry Pi 3 you need to change it to
```
CONFIG_PLATFORM_I386_PC = n
CONFIG_PLATFORM_ARM_RPI = n
CONFIG_PLATFORM_ARM_RPI3 = y
```

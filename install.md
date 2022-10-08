# Install

## 联网
### 有线
```
dhcpcd                                ## 启动dhcp获取网络
```
### 无线
```
iwctl                                        ## 第一步：进入环境

device list                                  ## 第二步：列出网卡设备

station wlan0 scan                           ## 第三步：扫描网络，wlan0为无线网卡，wlan0 为无线网卡号

station wlan0 get-networks                   ## 第四步：列出扫描到的网络，wlan0 为无线网卡号

station wlan0 connect SSID                   ## 第五步：连接无线网络，wlan0 为无线网卡号

quit                                         ## 第六步：退出iwd环境
```

## 更新系统时间
```
timedatectl set-ntp true                        ## 启动ntp

timedatectl status                              ## 查询时间状态
```

## 更新为国内镜像源
```
reflector --country China --age 72 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

## 分区和格式化
```
# 使用cfdisk对硬盘进行分区
cfdisk <install disk name>
```
```
# 格式化ESP分区
mkfs.fat -F32 /dev/nvme0n1p1

# 格式化文件分区
mkfs.f2fs -f -l ArchOS /dev/nvme0n1p2
```

## 挂载分区
```
mount /dev/nvme0n1p2 /mnt
```

## 安装
```
pacstrap /mnt base base-devel linux linux-firmware linux-headers

pacstrap /mnt vim                        ## 安装命令行编辑工具

pacstrap /mnt iwd                        ## 安装无线管理工具

pacstrap /mnt zsh                        ## 安装zsh
```

## 生成文件系统的表文件
```
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

## 进入新系统
```
arch-chroot /mnt
```

## 设置时区和同步当前时间
```
vim /etc/locale.gen

# 将以下两行取消注释(删除前面的井号)
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8

# 生成本地语言信息
locale-gen
```

## 设置本地语言环境变量
```
/etc/locale.conf
-------------------------
LANG=en_US.UTF-8
```

## efi启动配置
```
# 安装efibootmgr
pacman -Sy efibootmgr

# 挂载esp分区
mount /dev/nvme0n1p1 /mnt/

# 创建EFI文件夹
mkdir -p /mnt/EFI/Arch/
 
# 复制内核文件
cp /boot/* /mnt/EFI/Arch/

# 创建efi引导
efibootmgr --create --disk /dev/nvme0n1p1 --part 1 --label "Arch Linux Boot Manager" --loader /EFI/Arch/vmlinuz-linux --unicode 'root=UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rw initrd=/EFI/Arch/initramfs-linux.img'
```
## 设置root密码


## 重启

## 网络设置
```
# 开机自己iwd服务
systemctl enable iwd
systemctl start iwd

# 开启iwd dhcp服务 /etc/iwd/main.conf
[General]
EnableNetworkConfiguration=true

# 开启系统
systemctl enable systemd-resolved
systemctl start systemd-resolved
```

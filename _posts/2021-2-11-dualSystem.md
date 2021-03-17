---
layout: post
title: manjaro-windows双系统安装 
date: 2021-2-11
Author: halin
tag: [linux]
conmment: false
toc: true
---
UEFI下manjaro-windows双系统安装 
<!-- more -->
## 注意事项
 本文适用UEFI的双系统安装，不适合MBR，注意区分。
## 安装双系统前准备
1. 制作USB安装盘

    下载manjaro固件
    ```
    https://manjaro.org/download/
    ```
    U盘制作工具
    ```

    https://github.com/pbatard/rufus/releases/download/v3.13/rufus-3.13.exe
    ```
2. 进入bios检查以下选项
   - 关闭 Intel Optane memory
   - 关闭 RAID option
   - 关闭 Secure Boot（安全启动）
   - 关闭 Fast Boot（快速启动）
   - 切换 CSM (Legacy/MBR) boot（选择UEFI）
   - 启动 AHCI
## window分区
安装manjaro系统需要腾出32-64G及以上的未分区磁盘空间。

通过以下两种方法腾出未分区空间
- 直接删除某个盘
  - 右键win徽标，选择磁盘管理
  - 选择准备用作做manjaro系统的盘，右键删除卷即可
- 通过压缩卷拆分分区
    - 右键win徽标，选择磁盘管理
    - 选择准备拆分用作做manjaro系统的盘，右键压缩卷
    - 确定需要的大小，在32-64G及以上
    - 确定压缩即可

## 安装manjaro
1. 插入usb安装盘
2. 进入manjaro安装图形界面（Calamares）
3. 跟着指示一步步走，直到**分区**选项
4. 选择**手动分区**
5. 分成以下几个分区（建议）
   1. EFI分区（必须）
       - 大小 512 m
       - 文件系统 fat32
       - 挂载点 /boot/efi
       - 标记 boot
       - 确认
   2. SWAP空间（必须）
      - 大小为本机物理内存 10%-25%
      - 文件系统 linuxswap
      - 确认
   3. root空间（必须）
       - 大小 20480 m（最小建议空间）
       - 文件系统 ext4
       - 挂载点 / 
       - 确认
   4. home空间（非必要）
       - 大小默认值（即为剩余空间）
       - 文件系统 ext4
       - 挂载点 /home
       - 确定
6. 下一步
7. 直到安装完成，但不要重启
8. 打开terminal，输入 efibootmgr 
9. 查看manjaro是否在第一条
    - 在第一条就可以重启了
    - 不在第一条重启会直接进入windows,输入以下命令解决
         ```
         sudo mount /dev/sda5 /mnt
         sudo mount /dev/sda2 /mnt/boot/efi
         sudo cp /mnt/boot/grub/x86_64-efi/core.efi /mnt/boot/efi/EFI/boot/bootx64.efi
         sudo efibootmgr -c -d /dev/sda -p 2 -L "manjaro" -l "\EFI\Manjaro\grubx64.efi"

         ```


10. 如果已经直接进入了windows
    
    打开cmd输入以下命令解决
    ```
    bcdedit /set {bootmgr} path \EFI\manjaro\grubx64.efi
    ```
# BringUp OpenWRT to TP-Link D50 v1

The router needs small hardware modification to allow usage of openwrt.
This document is an guide with step by step instructions.

1. open cover and connect uart adapter<br>
2. setup tftp server
3. start router and check that uart adapter is working like expected
4. transfer and flash openwrt image

## open cover
You need to remove the two screws which are behind the label on the housing underside. You can remove the housing topside from the underside after that. note: some force is needed to open all the plastic latches

## setup tftp server
There are a lot of different tftp server available.
I used PUMKIN for windows which allows you an easy and painless setup:

Please download the tftp server application [5] and the Openwrt image [2] to your windows workstation. After Installation of PumpKIN you need to specify the "tftp filesystem root" (this is the location of your Openwrt image file).

## 3 - I added some pins to PCB of Router to allow connection with USB/UART adapter
see picture photos/uart_connected.jpg 


## 4 - flash procedures
The following instructions require a connection to the J2 UART interface.

note:
- you need tftp server for data transfer of openwrt image (openwrt-19.07.2-ath79-generic-tplink_archer-d50-v1-squashfs-sysupgrade.bin).
- ip address of the tftp server is 192.168.1.32 in my network.

### Flash instruction under U-Boot, using UART
------------------------------------------
```

# set the ip address of tp-link router
setenv ipaddr 192.168.1.101

# set the ip address of tftp router
setenv serverip 192.168.1.32

# transfer openwrt image from tftp server to RAM of router
tftpboot 0x81000000 openwrt-19.07.2-ath79-generic-tplink_archer-d50-v1-squashfs-sysupgrade.bin

# erase partition on router
erase 0x9f020000 +$filesize

# copy openwrt image from RAm to flash filesystem
cp.b 0x81000000 0x9f020000 $filesize

# restart router
reset
```

### Basic configuration of router
#### Set static IP adress
edit file /etc/config/network
```
config interface 'lan'
        option type 'bridge'
        option ifname 'eth0.1'
        option proto 'static'
        option ipaddr '192.168.1.101'
        option netmask '255.255.255.0'
        option ip6assign '60'
        list dns '8.8.8.8'
        option gateway '192.168.1.1'
```


# Bookmark

-> [1] patch with first support and flash instructions
   https://git.openwrt.org/?p=openwrt/openwrt.git;a=commit;h=f5d2c91415a68f554815860d574145644fc31c16

-> [2] download image
   http://downloads.openwrt.org/releases/19.07.2/targets/ath79/generic/openwrt-19.07.2-ath79-generic-tplink_archer-d50-v1-squashfs-sysupgrade.bin

-> [3] OpenWrt Forum "Archer D50 Support"
   https://forum.openwrt.org/t/archer-d50-support/38613

-> [4] OpenWrt Techdata TP-Link Archer D50 v1
   https://openwrt.org/toh/hwdata/tp-link/tp-link_archer_d50_v1

-> [5] Pumpkin TFTP Server for Windows
   http://kin.klever.net/dist/pumpkin-2.7.3-exe.zip

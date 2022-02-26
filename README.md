# OpenWRT on Cisco Meraki MR33

You can find the associated article [here](https://blog.jbriault.fr/installer-openwrt-sur-cisco-meraki-mr33/) (in French).

## Install the prerequisites

If you have a RaspberryPI with the latest OS version, you will have to install `pip2` manually as it is no longer present in the repositories.

```bash
sudo apt update; sudo apt install python2 curl -y
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
sudo python2 get-pip.py

# Check
pip2 --version
```

Install *pyserial* for `ubootwrite.py` script :

```bash
pip2 install pyserial
```

## Upload uboot to MR33

To do this nothing very complicated, you must use the following commands:

```bash
cd mr33-openwrt/ubootwrite
chmod a+x ubootwrite.py
./ubootwrite.py --write=mr33-uboot.bin --serial=/dev/ttyS0
```

## Upload initramfs image to MR33

You need to connect your raspberry with an IP address in the 192.168.1.0/24 subnet so that you can access the terminal which now has the IP (192.168.1.1).
Now we need to send the image with the following command:

```bash
echo -e "openwrt-ipq40xx-meraki_mr33-initramfs-fit-uImage.itb" | tftp 192.168.1.1
```

All you have to do is connect via SSH to confirm that OpenWRT is present. Be careful not to restart the terminal, otherwise you will have to start all over again, for the moment, we have not written anything in the NVRAM.

```bash
ssh root@192.168.1.1
root@OpenWrt:~#
```

## Plan a rollback

Since we have not yet made a backup of the OS already present, and we have not overwritten the NVRAM, we need to do so to be able to back up if this is the case.

To do this, use the following commands:

```bash
file="openwrt-ipq40xx-meraki_mr33-initramfs-fit-uImage.itb"
size=$(cat "$file" | wc -c)
ubirename /dev/ubi0 part.old part.meraki.old
ubimkvol /dev/ubi0 --size=$size --type=static 
--name=part.old

Volume ID 99, size 49 LEBs (6221824 bytes, 5.9 MiB), LEB size 126976 bytes (124.0 KiB), static, name
"part.old", alignment 1
Ubimkvol generates a new Volume ID (marked in red). This ID is used for the next command so please
replace the 99 with the correct value.

ubiupdatevol /dev/ubi0_99 "$file"
```

Note that `/dev/ubi0_99` corresponds to `Volume ID 99` above, so this number may change depending on the case. 

## Install OpenWRT forever

Now that we have prepared a backtrack, we will be able to install OpenWRT, for that we have to copy the file `openwrt-ipq40xx-meraki_mr33-squashfs-sysupgrade.bin` on our machine with the command:

```bash
scp openwrt-ipq40xx-meraki_mr33-squashfs-sysupgrade.bin root@192.168.1.1:/root/
```

It will remain on the machine to use the commands:

```bash
ubirename /dev/ubi0 part.safe part.meraki
sysupgrade -v openwrt-ipq40xx-meraki_mr33-squashfs-sysupgrade.bin
```

Once this is done, the terminal will reboot again, and you can then connect to the OpenWRT web interface (via http://192.168.1.1).

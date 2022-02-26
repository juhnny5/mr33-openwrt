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
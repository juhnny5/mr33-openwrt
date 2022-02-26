# OpenWRT on Cisco Meraki MR33

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

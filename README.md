# Raspberry Pi Hacking Unit

## Setup

## Flashing the SD Card

```sh
$ diskutil list
$ diskutil unmountDisk /dev/disk<disk# from diskutil>
$ sudo dd bs=1m if=<path to .img> of=/dev/rdisk<disk# from diskutil> conv=sync
```

This process takes a while. While waiting, you can send a `SIGINFO` signal by pressing `Ctrl+T` to track the progress. After the image is copied over, you are done. You can insert the SD card to your Pi and boot it up.

## Kali Linux

> https://www.offensive-security.com/kali-linux-arm-images/#1493408272250-e17e9049-9ce8

### Reconfigure SSH Keys

```sh
$ cd /etc/ssh/
$ dpkg-reconfigure openssh-server
$ update-rc.d -f ssh remove
$ update-rc.d -f ssh defaults
$ nano /etc/ssh/sshd_config

# Uncomment `PermitRootLogin yes`

$ service ssh restart
$ update-rc.d -f ssh enable 2 3 4 5
```

### Automatic Login

```sh
$ nano /etc/lightdm/lightdm.conf

# Set the following in the file.
# autologin-user=root
# autologin-user-timeout=0

$ nano /etc/pam.d/lightdm-autologin
# Comment out `auth      required pam_succeed_if.so user != root quiet_success`
```

```sh
$ nano /etc/gdm3/daemon.conf

# Uncomment the lines under `Enabling automatic login
```

### Fix Overscan

```sh
$ nano /boot/config.txt

# Uncomment `disable_overscan=1`
```

### Change Boot Sequence

Get the current session type:

```sh
$ systemctl get-default
```

To boot into X:

```sh
$ systemctl set-default graphical.target
```

To boot into text-mode:

```sh
$ systemctl set-default multi-user.target
```

### Resize Partition

```sh
$ apt-get install gparted
$ gparted

# Select ext4 partition and click "Resize/Move", then move slider all the way to the right so that "Free spacie following (MiB):" is 0.
```

## Ubuntu Server

> https://ubuntu.com/download/iot/raspberry-pi

### Configuring Onboard WiFi

Update the system:

```sh
$ sudo apt update
$ sudo apt full-upgrade
$ sudo reboot
```

Determine interface names:

```sh
$ ip link show

# 1: lo: <LOOPBACK,UP,LOWER_UP> …
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> … state UP …
# 3: wlan0: <BROADCAST,MULTICAST> … state DOWN
```

Edit cloud-init config:

```sh
$ cd /etc/netplan
$ sudo nano 50-cloud-init.yaml
```

Add WiFi access information to the cloud-init config YAML file:

```yaml
network:
    version: 2
    ethernets:
        eth0:
            optional: true
            dhcp4: true
    # add wifi setup information here ...
    wifis:
        wlan0:
            optional: true
            access-points:
                "YOUR-SSID-NAME":
                    password: "YOUR-NETWORK-PASSWORD"
            dhcp4: true
```

## Change Hostname

Get the hostname:

```sh
$ hostname
```

Change the hostname temporarily until next reboot:

```sh
$ sudo hostname <name>
```

Change the hostname permanently:

```sh
$ sudo nano /etc/hostname

# Change the name in the file
```

## Allow Local Devices to Access Pi with Hostname

Install `avahi` and `insserv`:

```sh
```

## Common Commands

- `startx`: Enter graphical mode
- `shutdown now`: Shuts down the Raspberry Pi immediate
- `reboot`: Reboots the Raspberry Pi

## Connecting to the Pi via SSH

### Via Wi-Fi

Just make sure you are on the same network as the Pi, then connect to it using either one of the following methods:
1. By IP: `ssh pi@<ip_address>` (you can scan for all devices connected to your network via `arp -a`)
2. By host name: `ssh pi@<host_name>` (you can get the host name of the Pi by `hostname -f` inside the Pi's Shell)

### Via Ethernet Cable

Assuming you are on a Mac, first, enable network sharing on the ethernet cable:
1. Go to **System Preferences** -> **Sharing**
2. Select **Internet Sharing** on the left (just select, don't check, you probably can't yet)
3. On the right, leave **Share your connection from:** as `Wi-Fi`, then select the port corresponding to your Ethernet cable (i.e. `USB 10/100/1000 LAN`, hint: you can find out the name of the port by first connecting your Ethernet cable to your Mac then go to **System Preferences** -> **Network** and see which one is newly enabled)
4. Check the checkbox for **Internet Sharing**
5. Go to **System Preferences** -> **Network** and see that there is a newly enabled network, ensure that the **Configure IPv4** option is **Using DHCP**
6. From Terminal, run `ifconfig` and look for your IP address (aliased `inet`) for the `bridge100` option (i.e. `192.168.2.1`)
7. Scan for connected devices with the above IP address by running `nmap -n -sP <ip_address>/24`
8. Note that there should be multiple scanned results, with the first one being your own device (i.e. `192.168.2.1`), and the other one (or one of the other ones) being the Pi
9. Simply connect to it via `ssh pi@<ip_address>`

## Troubleshooting

### If the Pi is stuck in readonly mode...

Run `fschk`, this usually fixes the problem.

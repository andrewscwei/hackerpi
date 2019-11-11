# Raspberry Pi Hacking Unit

## Common Commands

- `startx`: Enter graphical mode
- `shutdown now`: Shuts down the Raspberry Pi immediate
- `reboot`: Reboots the Raspberry Pi

## Changing Boot Sequence

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
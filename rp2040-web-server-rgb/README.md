# Control Built-in RGB LED over Wi-Fi with Nano RP2040 Connect

Learn how to set up your board as a web server, allowing other clients to connect via browser, to control and monitor data.

<https://docs.arduino.cc/tutorials/nano-rp2040-connect/rp2040-web-server-rgb/>

## Sketch

1. Open `rp2040-web-server-rgb.ino`
2. Boad setup [once]
    1. Open Board Manager
    2. Search 'Arduino Mbed OS Nano Boards'
    3. Install (latest version 4.2.1)
3. Wifi library [once]
    1. Open Library Manager
    2. Search 'WiFiNINA'
    3. Install (latest version 1.8.14)
4. Wifi configuration
    1. New Tab `wifi_secrets.h`
    2. Set network name and password (content below)
5. Deploy
    1. Verify
    2. Upload
6. Open board IP Address on a Browser (HTTP)

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

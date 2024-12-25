# Arduino IDE 2 on Ubuntu 24 (Noble, Oracular)

<https://github.com/arduino/arduino-ide>

<https://github.com/arduino/arduino-ide/releases>

<https://github.com/arduino/arduino-ide/releases/tag/2.3.4>

## Install

<https://docs.arduino.cc/software/ide-v2/tutorials/getting-started/ide-v2-downloading-and-installing/#linux>

<https://askubuntu.com/questions/1515105/sandbox-problems-with-arduino-ides-with-24-04>

```bash
grep \.local ~/.bashrc

# export PATH=$HOME/.local/bin:$PATH

curl -LO https://downloads.arduino.cc/arduino-ide/arduino-ide_2.3.4_Linux_64bit.zip

mkdir -p ~/.local/arduino-ide_2.3.4_Linux_64bit/

unzip -d ~/.local/arduino-ide_2.3.4_Linux_64bit/arduino-ide_2.3.4_Linux_64bit.zip

rm arduino-ide_2.3.4_Linux_64bit.zip

mkdir -p ~/.local/bin

ln -s ~/.local/arduino-ide_2.3.4_Linux_64bit/arduino-ide ~/.local/bin/arduino-ide

echo 'SUBSYSTEMS=="usb", ATTRS{idVendor}=="2341", GROUP="plugdev", MODE="0666"' > 99-arduino.rules

sudo mv 99-arduino.rules /etc/udev/rules.d/

# FATAL:setuid_sandbox_host.cc(158)] The SUID sandbox helper binary was found, but is not configured correctly. Rather than run without sandboxing I'm aborting now.
sudo sysctl -w kernel.apparmor_restrict_unprivileged_userns=0

arduino-ide

# ...
```

## Board Package

Arduino Nano RP2040 Connect

<https://docs.arduino.cc/software/ide-v2/tutorials/ide-v2-board-manager/#mbed-os-nano>

<https://github.com/arduino/ArduinoCore-mbed>

<https://github.com/arduino/ArduinoCore-mbed/releases>

<https://github.com/arduino/ArduinoCore-mbed/releases/tag/4.2.1>

1. Open Board Manager
2. Search 'Arduino Mbed OS Nano Boards'
3. Install (latest version 4.2.1)

```text
Downloading packages
arduino:arm-none-eabi-gcc@7-2017q4
arduino:bossac@1.9.1-arduino2
arduino:dfu-util@0.10.0-arduino1
arduino:openocd@0.11.0-arduino2
arduino:rp2040tools@1.0.6
arduino:mbed_nano@4.2.1
Installing arduino:arm-none-eabi-gcc@7-2017q4
Configuring tool.
arduino:arm-none-eabi-gcc@7-2017q4 installed
Installing arduino:bossac@1.9.1-arduino2
Configuring tool.
arduino:bossac@1.9.1-arduino2 installed
Installing arduino:dfu-util@0.10.0-arduino1
Configuring tool.
arduino:dfu-util@0.10.0-arduino1 installed
Installing arduino:openocd@0.11.0-arduino2
Configuring tool.
arduino:openocd@0.11.0-arduino2 installed
Installing arduino:rp2040tools@1.0.6
Configuring tool.
arduino:rp2040tools@1.0.6 installed
Installing platform arduino:mbed_nano@4.2.1
Configuring platform.

You might need to configure permissions for uploading.
To do so, run the following command from the terminal:
sudo "/home/cavani/.arduino15/packages/arduino/hardware/mbed_nano/4.2.1/post_install.sh"


Platform arduino:mbed_nano@4.2.1 installed
```

```bash
sudo "/home/cavani/.arduino15/packages/arduino/hardware/mbed_nano/4.2.1/post_install.sh"

# Reload rules...
```

## Libraries

1. Open Library Manager
2. Search 'WiFiNINA'
3. Install (latest version 1.8.14)

```text
Downloading WiFiNINA@1.8.14
WiFiNINA@1.8.14
Installing WiFiNINA@1.8.14
Installed WiFiNINA@1.8.14
```

## Sketch

<https://docs.arduino.cc/tutorials/nano-rp2040-connect/rp2040-web-server-rgb/>

1. New Sketch `rp2040-web-server-rgb`
2. New tab `wifi_secrets.h`
3. Verify
4. Upload

Content:

`rp2040-web-server-rgb.ino`

```c
#include <SPI.h>
#include <WiFiNINA.h>
#include "wifi_secrets.h"

int status = WL_IDLE_STATUS;
WiFiServer server(80);

void setup() {
  pinMode(LEDR, OUTPUT);
  pinMode(LEDG, OUTPUT);
  pinMode(LEDB, OUTPUT);
  Serial.begin(9600);  // initialize serial communication

  // check for the WiFi module:
  if (WiFi.status() == WL_NO_MODULE) {
    Serial.println("Communication with WiFi module failed!");
    // don't continue
    while (true)
      ;
  }

  String fv = WiFi.firmwareVersion();
  if (fv < WIFI_FIRMWARE_LATEST_VERSION) {
    Serial.println("Please upgrade the firmware");
  }

  // attempt to connect to WiFi network:
  while (status != WL_CONNECTED) {
    Serial.print("Attempting to connect to Network named: ");
    Serial.println(ssid);  // print the network name (SSID);

    // Connect to WPA/WPA2 network. Change this line if using open or WEP network:
    status = WiFi.begin(ssid, pass);
    // wait 10 seconds for connection:
    delay(10000);
  }
  server.begin();     // start the web server on port 80
  printWifiStatus();  // you're connected now, so print out the status
}


void loop() {
  WiFiClient client = server.available();  // listen for incoming clients

  if (client) {                    // if you get a client,
    Serial.println("new client");  // print a message out the serial port
    String currentLine = "";       // make a String to hold incoming data from the client
    while (client.connected()) {   // loop while the client's connected
      if (client.available()) {    // if there's bytes to read from the client,
        char c = client.read();    // read a byte, then
        Serial.write(c);           // print it out the serial monitor
        if (c == '\n') {           // if the byte is a newline character

          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println();

            // the content of the HTTP response follows the header:
            client.print("<style>");
            client.print(".container {margin: 0 auto; text-align: center; margin-top: 100px;}");
            client.print("button {color: white; width: 100px; height: 100px;");
            client.print("border-radius: 50%; margin: 20px; border: none; font-size: 20px; outline: none; transition: all 0.2s;}");
            client.print(".red{background-color: rgb(196, 39, 39);}");
            client.print(".green{background-color: rgb(39, 121, 39);}");
            client.print(".blue {background-color: rgb(5, 87, 180);}");
            client.print(".off{background-color: grey;}");
            client.print("button:hover{cursor: pointer; opacity: 0.7;}");
            client.print("</style>");
            client.print("<div class='container'>");
            client.print("<button class='red' type='submit' onmousedown='location.href=\"/RH\"'>ON</button>");
            client.print("<button class='off' type='submit' onmousedown='location.href=\"/RL\"'>OFF</button><br>");
            client.print("<button class='green' type='submit' onmousedown='location.href=\"/GH\"'>ON</button>");
            client.print("<button class='off' type='submit' onmousedown='location.href=\"/GL\"'>OFF</button><br>");
            client.print("<button class='blue' type='submit' onmousedown='location.href=\"/BH\"'>ON</button>");
            client.print("<button class='off' type='submit' onmousedown='location.href=\"/BL\"'>OFF</button>");
            client.print("</div>");

            // The HTTP response ends with another blank line:
            client.println();
            // break out of the while loop:
            break;
          } else {  // if you got a newline, then clear currentLine:
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }

        // Check to see if the client request was /X
        if (currentLine.endsWith("GET /RH")) {
          digitalWrite(LEDR, HIGH);
        }
        if (currentLine.endsWith("GET /RL")) {
          digitalWrite(LEDR, LOW);
        }
        if (currentLine.endsWith("GET /GH")) {
          digitalWrite(LEDG, HIGH);
        }
        if (currentLine.endsWith("GET /GL")) {
          digitalWrite(LEDG, LOW);
        }
        if (currentLine.endsWith("GET /BH")) {
          digitalWrite(LEDB, HIGH);
        }
        if (currentLine.endsWith("GET /BL")) {
          digitalWrite(LEDB, LOW);
        }
      }
    }
    // close the connection:
    client.stop();
    Serial.println("client disconnected");
  }
}

void printWifiStatus() {
  // print the SSID of the network you're attached to:
  Serial.print("SSID: ");
  Serial.println(WiFi.SSID());

  // print your board's IP address:
  IPAddress ip = WiFi.localIP();
  Serial.print("IP Address: ");
  Serial.println(ip);

  // print the received signal strength:
  long rssi = WiFi.RSSI();
  Serial.print("signal strength (RSSI):");
  Serial.print(rssi);
  Serial.println(" dBm");
  // print where to go in a browser:
  Serial.print("To see this page in action, open a browser to http://");
  Serial.println(ip);
}
```

`wifi_secrets.h`

```c
char ssid[] = "your_wifi_network_name";
char pass[] = "your_wifi_password";
```

## How to Use (Example)

- Create a Hotspot on a mobile phone
- Upload sketch with phone hotspot network name and password
- Connect a Power bank on the board
- Control the board from the phone browser [*]

[*] use Net Analyser app to scan for the board IP address

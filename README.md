# CSN150-Project-ESP32-CAM-Soft-Access-Point-AP-Web-Server

## Purpose
Set up the ESP32-CAM and Arduino IDE environment, configure the `CameraWebServer` sketch to operate as a standalone Soft Access Point (`WiFi.softAP`), and stream live camera video directly to a client device at `http://192.168.4.1` without an external wireless router.

## Equipment
- [ESP32Cam](https://www.amazon.com/Aideepen-ESP32-CAM-Bluetooth-ESP32-CAM-MB-Arduino/dp/B08P2578LV/ref=sr_1_3?crid=4FY0ECFW0ZX7&keywords=ESP32+Cam&qid=1678902050&sprefix=esp32+cam%2Caps%2C240&sr=8-3)

- [USB Micro Data Cable](https://www.amazon.com/AmazonBasics-Male-Micro-Cable-Black/dp/B0711PVX6Z/ref=sr_1_1_sspa?keywords=micro+usb+data+cable&qid=1678902214&sprefix=Micro+USB+data+%2Caps%2C89&sr=8-1-spons&psc=1&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFaU0NaUVZHU1RFUlAmZW5jcnlwdGVkSWQ9QTA3NTA4MDVFVERCS01HVlgxM1YmZW5jcnlwdGVkQWRJZD1BMDE4NTE1NTIwWUdONkdWSzU1M1Amd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl)

## Links to documentation and tools

##### Video 1: 

##### Other Links: 
- [Random Nerd Tutorials – ESP32-CAM: Set Access Point (AP) for Web Server (Arduino IDE)](https://randomnerdtutorials.com/esp32-cam-access-point-ap-web-server/)
- [Espressif Arduino-ESP32 Board Manager Index](https://espressif.github.io/arduino-esp32/package_esp32_index.json)

##### AI GPTs used
- Google Gemini (assisted with code modifications in `CameraWebServer.ino` and `board_config.h`, upload workflow, and troubleshooting)

## Steps I followed
1. Opened Arduino IDE (v2.3.11) on Linux and verified the `esp32` by Espressif Systems board package was installed in **Boards Manager**.
2. Connected the ESP32-CAM to the computer via USB and selected **Tools → Board → esp32 → AI Thinker ESP32-CAM**.
3. Selected the active serial port **`/dev/ttyUSB0`** under **Tools → Port**.
4. Opened the built-in camera server example via **File → Examples → ESP32 → Camera → CameraWebServer**.
5. Opened the **`board_config.h`** tab and verified that `#define CAMERA_MODEL_AI_THINKER` was uncommented while all other camera models remained commented out (`//`).
6. Set the Soft-AP hotspot network name (`ssid`) and password (minimum 8 characters) on lines 13–14 of **`CameraWebServer.ino`**.
7. Replaced the Station-mode `WiFi.begin(ssid, password)` connection loop in `void setup()` with `WiFi.softAP(ssid, password);` (line 111) so the ESP32-CAM broadcasts its own standalone Wi-Fi network.
8. Updated the Serial Monitor IP printout in `void setup()` (line 116) from `Serial.print(WiFi.localIP());` to `Serial.print(WiFi.softAPIP());` so it displays the Soft-AP IP address (`192.168.4.1`).
9. Put the board into flashing mode (`IO0` connected to `GND`), uploaded the sketch to the ESP32-CAM, and confirmed `Done uploading.` in the Output console.
10. Disconnected the `IO0`-to-`GND` jumper wire and pressed the **RST** button so the board booted into normal run mode.
11. Opened the **Serial Monitor** at **`115200` baud** and confirmed the output: `Camera Ready! Use 'http://192.168.4.1' to connect`.
12. Connected a client device to the ESP32-CAM's Wi-Fi Access Point, navigated to `http://192.168.4.1` in a web browser, and clicked **Start Stream** to verify the live camera feed.

## Problems and Solutions

**Problem:** `E (485) camera: Camera probe failed with error 0x105(ESP_ERR_NOT_FOUND)` / `Camera init failed with error 0x105`  
**Solution:** Confirmed `#define CAMERA_MODEL_AI_THINKER` was active in `board_config.h` so the correct camera GPIO pins were mapped, reseated the OV2640 camera ribbon cable firmly inside the CSI connector latch, and powered the board with a stable 5V supply.

**Problem:** Station-mode sketch (`HelloServer_Seidu.ino`) was initially open instead of the camera server, and camera model definitions were located in `board_config.h` rather than the main `.ino` file.  
**Solution:** Opened `File → Examples → ESP32 → Camera → CameraWebServer`, navigated to the `board_config.h` tab, and enabled `#define CAMERA_MODEL_AI_THINKER` on line 23.

**Problem:** Serial Monitor displays `http://0.0.0.0` in Access Point mode, and the board shows only a solid red power LED without broadcasting Wi-Fi if left in programming mode.  
**Solution:** Changed `WiFi.localIP()` to `WiFi.softAPIP()` in `CameraWebServer.ino` so the true Soft-AP IP (`192.168.4.1`) is printed, then removed the `IO0`-to-`GND` jumper wire and pressed **RST** after uploading to exit download mode and start the Wi-Fi hotspot.

### Example Problem
**Problem:** Arduino code will not load on ESP32 Cam.  
**Solution:** Camera drivers were incorrect I needed to install the driver: [https://www.wch-ic.com/downloads/CH341SER_ZIP.html](https://github.com/martin-ger/esp32_nat_router). I used file, "CH341SER.ZIP" and it worked.

## Final Report
In this project, I configured the Arduino IDE (v2.3.11) on Linux to program an AI Thinker ESP32-CAM as a standalone Soft Access Point (Soft-AP) video streaming web server. After selecting the AI Thinker ESP32-CAM board on port `/dev/ttyUSB0` and enabling `#define CAMERA_MODEL_AI_THINKER` in `board_config.h`, I modified `CameraWebServer.ino` to use `WiFi.softAP(ssid, password)` and `WiFi.softAPIP()` instead of connecting to an external router.

After uploading the sketch and resetting the board into normal run mode, I verified in the Serial Monitor at `115200` baud that the camera initialized cleanly and published the web interface at `http://192.168.4.1`. Connecting to the ESP32-CAM's Wi-Fi hotspot and opening `http://192.168.4.1` in a browser successfully displayed the live video stream, confirming that the ESP32-CAM functioned properly as an independent wireless camera server.

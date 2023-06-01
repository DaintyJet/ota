# Basic OTA via WiFi on ESP32

This project demonstrates how to use the Over The Air (OTA) update system on an ESP32 board. It also shows the danger of OTA without security. If security is not considered for the OTA, an adversary may inject a malicious firmware into a victim device through the OTA. For secure OTA updates through https, refer to [Get Started With Secure ESP32 OTA through HTTPS](https://github.com/PBearson/Get-Started-With-ESP32-OTA).


## Setup
1. If it is not already configured, It is necessary to configure the Ubuntu VM to use the bridged adaptor. This can be changed using the steps below
   * VirtualBox Manager -> Ubuntu IoT -> Settings -> Network -> Adapter 1 -> Bridged Adapter

2. If not installed, open a terminal in Ubuntu, and type the following command to install networking tools.
   ```sh 
   sudo apt install net-tools
   ```

   Once we have finished installing *net-tools* we can use them the IP address of Ubuntu with the following command
   ```sh
   sudo ifconfig
   ```

3. Clone this GitHub project within a folder on the Ubuntu VM.

   For example, the following commands in a terminal delete the old *ota* folder and clones the GitHub project to */home/iot/Documents*

   ```sh
   cd ~/Documents                                  # Change to the folder Documents within the home folder
   rm -rf ota                                      # Delete the old ota folder
   git clone https://github.com/xinwenfu/ota.git   # Clone the GitHub repository
   ```
   The # symbol indicates the rest of the line is comment.

4. Open VS Code and import this GitHub project, which is an Arduino project.
   - Click the PlatformIO icon
   - Click *QUICK ACCESS* -> *PIO Home* -> *Open*
   - Click *Import Arduino Project*
   - Select a board: *Espressif ESP32 Dev Module*
   - *Choose a directory with existing Arduino IDE project*: *Places* -> *iot* -> *Documents* -> *ota*
   - Click the button *Import*

## Flashing the board for the first time via micro USB

1. Within VS Code, update the file `wifi_credentials.h` with your SSID and PASSWORD. 
   ```c
   #define SSID "YOUR_SSID"
   #define PASSWORD "YOUR_PASSWORD"
   ```
2. Build the project.

3. Upload the firmware via micro USB.
   - The first installation of the firmware will need to be over the micro USB cable. The code that is built contains OTA functionality and once flashed into the SaTC ESP32 PCB board will be running and waiting for later OTA requests.
   - Press and hold the BOOT button (IO0) of the ESP32 board until the upload starts, in some cases this may not be needed.

4. The newly uploaded firmware blinks the blue LED on the board at a regular interval.
   - The ESP32 board is ready for upgrading through OTA if the blue LED on the board blinks

5. Use the Serial Monitor to see the output from the ESP board firmware, which prints out the IP address of the board

## OTA via WiFi

1. Change the code, e.g, the frequency of the LED light blinking. In the code snippet below, the *delay* function controls the frequency of the blinking.

   ```c
   digitalWrite(LED_BUILTIN, HIGH);   // turn the LED on (HIGH is the voltage level)
   delay(1000);                       // wait for a second
   digitalWrite(LED_BUILTIN, LOW);    // turn the LED off by making the voltage LOW
   delay(1000);                       // wait for a second
   ```

2. In the platformio.ini file, specify the upload_protocol and upload_port, which is the ip address of the board. One example platformio.ini looks like the following. Pay attention to the last two lines.
    - Optionally, unplug the micro USB cable from the computer and plug it into another USB port. The purpose is to show we do not need to upload the firmware via micro USB now. 

   ```conf
   ; PlatformIO Project Configuration File
   ;
   ;   Build options: build flags, source filter
   ;   Upload options: custom upload port, speed and extra flags
   ;   Library options: dependencies, extra library storages
   ;   Advanced options: extra scripting
   ;
   ; Please visit documentation for the other options and examples
   ; https://docs.platformio.org/page/projectconf.html

   [env:esp32dev]
   platform = espressif32
   board = esp32dev
   framework = arduino

   monitor_speed = 115200     ; The serial monitor speed must be right to see the output
   upload_protocol = espota   ; Upload via OTA. 
   upload_port = 192.168.1.7  ; Change the IP to IP of the ESP32 board
   ```

3. Build the project.
4. Upload the firmware via OTA. You will see a different uploading process from the uploading process via micro USB


## Notes

This project is based on https://github.com/atomic14/ESP32Ota.
Click the image to watch the original demo video with Arduino IDE.

[![Demo Video](https://img.youtube.com/vi/_bMsrxiyuHs/0.jpg)](https://www.youtube.com/watch?v=_bMsrxiyuHs)

In the Arduino IDE, once the sketch is running you can go to "Tools" -> "Ports" and you should find your ESP32 listed as "esp_test". You can change the name of your device by changing this line of code:
```
ArduinoOTA.setHostname("esp_test");
```


# SensorTag FPV

App that analyses FPV drone freestyle with the TI SensorTag CC2650. This was my final project for the 4B25 Embedded Systems module at the University of Cambridge.

## Getting Started

These instructions will get you a copy of the app running on your phone for testing and development purposes.

### Prerequisites

**Required**
* [SensorTag CC2650](http://www.ti.com/ww/en/wireless_connectivity/sensortag/) - Embedded system used
* [EvoThings Studio](https://evothings.com/download/) - Host used for Javascript applications and interfacing with the SensorTag
* [EvoThings App](https://play.google.com/store/apps/details?id=com.evothings.evothingsviewer) - Mobile application used to connect with the SensorTag

**Optional**
These items are only required if your SensorTag does not have the original firmware on it. Please see the section called 'Flashing SensorTag firmware' for more details.
* [SensorTag DevPack](http://www.ti.com/tool/CC-DEVPACK-DEBUG) - used for debugging and flashing firmware on the SensorTag
* [Code Composer Studio (CCS) v7.4.0](http://www.ti.com/tool/CCSTUDIO) - Used to load and develop SensorTag firmware
* [BLE-STACK v2.2.1](http://www.ti.com/tool/BLE-STACK-ARCHIVE) - Bluetooth low energy tools


### Installing the app

Firstly, follow the EvoThings instructions [here](https://evothings.com/download/) in order to setup the required environment. Next, clone this repository and simply drag and drop the `index.html` file into the `My Apps` section of the desktop studio. 

### Running the app

Click `Run` once connected to the EvoThings app and the 'SensorTag FPV Drone App' will load on your screen. Press the right button on the SensorTag to put it into discovery mode and hit the `Connect` button in the app. You should now see the 3D model of the drone moving as you change the orientation of the SensorTag! 

## Functionality

The app provides the following features
* Easy connect button to start communication with the SensorTag via BLE.
* Freestyle Analysis - this section gives the user comments on their aerobatic manoeuvres such as 'Great right roll' or 'Spectacular power loop'. It can also detect more complex moves such as a split S.
* Drone model - a 3D representation of the drone is rendered in the app and shows real time changes in orientation. This is possible by using the gyroscope readings and the drift is accounted for by using the accelerometer to calculate the tilt when the SenorTag is stationary. 
* Sensor Readings - the measurements from the accelerometer, gyroscope and magnetometer are given for each axis.

## Directory Structure
The differences of the structure to the blank EvoThings applications are the contents of `index.html`, new images and the addition of the JavaScript library `three.js` used to render 3D models.

    .
    ├── libs/                  # JavaScript and EvoThings libraries
    │   ├── evothings/         # Evothings libraries for interfacing with the SensorTag
    │   └── js/                # JavaScript libraries such as Three.js for 3D rendering
    ├── model/                 # .dae file which defines the drone 3D model
    ├── res/                   # Screenshots of the app
    ├── ui/                    # User interface - css files and images
    ├── README.md              # Getting started information
    ├── evothings.json         # Metadata for the EvoThings studio
    ├── image.png              # Drone image used in the app
    └── index.html             # Heart of the application. Layout and functionality all configured here.


## Flashing SensorTag firmware

### Option 1

If your SensorTag no longer has the factory firmware loaded on it, you will have to reflash using the Code Composer Studio (CCS) which can be downloaded [here](http://www.ti.com/tool/CCSTUDIO). Choose `v7.4.0` and the off-line installer is recommended. Run the installer and choose all default options (including installation location) but for `Processor Support` please make sure you pick `SimpleLink CC13xx and CC26xx Wireless MCUs`.

Download BLE-STACK `v2.2.1` from [here](http://www.ti.com/tool/BLE-STACK-ARCHIVE). You will have to create an account with Texas Instruments and agree to the terms. The executable is only Windows but there are ways to install on a Linux machine and the steps to do so are given at the end of this document.

When you launch CCS you will be prompted to install a new component called TI RTOS. Accept and let the program restart. In addition, install the ARM compiler tools 5.2.6 (found under `Help`, `Install new software`, then untick `Show only latest versions`). 

Now import the projects from the following locations.
```
C:/ti/simplelink/ble_sdk_2_02_01_18/examples/util/bim_extflash/cc2640/ccs
C:/ti/simplelink/ble_sdk_2_02_01_18/examples/cc2650stk/sensortag
```

Ensure you check boxes labeled `Automatically import...` and `Copy projects...`.

There should now be three projects in the `Project Explorer`.
1. BimExtflash
2. SensortagStack
3. SensortagApp
Connect the SensorTag to the computer via the DevPack and now right click on `Rebuild` and `Debug as` for each project in the order given above. 

### Option 2

An alternative option to get the factory firmware is to flash a hex file with TI SmartRF Flash Programmer 2 which can be downloaded [here](http://www.ti.com/tool/FLASH-PROGRAMMER). Once the BLE-STACK has been installed as explained in the previous section, the path of the required hex file is
```
ti/simplelink/ble_sdk_2_02_01_18/examples/hex/cc2650stk_sensortag_rel.hex
```

## Installing CCS on a Linux VM
It is highly recommend to use Windows instead of a Linux VM on a Mac. Everything works almost seamlessly. In addition, I ran into a driver problem as outlined below which meant I couldn't flash the SenorTag. 

**VirtualBox**
Installed VirtualBox [here](https://www.virtualbox.org/wiki/Downloads)
* VirtualBox 5.2.22 (OS X hosts)
* VirtualBox 5.2.22 Oracle VM VirtualBox Extension Pack (All supported platforms)
Installed VM with Ubuntu image from [here](http://releases.ubuntu.com/16.04/)
* Image: `ubuntu-16.04.5-desktop-amd64.iso`
* Default setup with minimum of 20gb storage

**CCS**
Download CCS v7.4.0 from [here](processors.wiki.ti.com/index.php/Download_CCS#) and extract.

```
sudo apt-get install libc6:i386
sudo apt-get update
cd Downloads
chmod +x ccs_setup_linux64_7.4.0.00015.bin
./ccs_setup_linux64_7.4.0.00015.bin
```
Same installation options as given in the previous section.

**WINE**
Download Wine to emulate windows so the BLE-STACK can be installed.

```
sudo dpkg --add-architecture i386
wget -nc https://dl.winehq.org/wine-builds/Release.key
sudo apt-key add Release.key
sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main'
sudo apt-get update
sudo apt-get install --install-recommends winehq-stable
winecfg
```

**BLE-STACK**
Download BLE-STACK `v2.2.1` from [here](http://www.ti.com/tool/BLE-STACK-ARCHIVE). and extract.

```
cd Downloads
wine ble_sdk_2_02_01_18_setup.exe
sudo apt install git
git clone https://github.com/jcormier/TI_BLE_CC2650_Linux_Convert.git
./convert_ti_ble_sdk_2_02_01_18_to_linux.sh ~/ti/ erase_it_all_and_copy_from_wine
```

See steps in course notes (D.4) to configure CCS.

**Driver Problem**
Error message when testing connection: “An attempt to connect to the XDS110 failed.”
CCS cannot seem to communicate with SensorTag through VM.
Tried fixing by installing the VirtualBox Extension Pack and adding the USB device in the filter section. The problem still persisted. Relevant forum thread [here](https://stackoverflow.com/questions/26474687/failed-to-attach-the-usb-device-segger-j-link-0100-to-the-virtual-machine-wind) and [here](http://e2e.ti.com/support/wireless-connectivity/bluetooth/f/538/p/409253/1543581).


## Acknowledgments

This project was supported by Phillip Stanley-Marbell during the 4B25 Embedded Systems course at the University of Cambridge.

# aia-poker-buddy
This repository is home for the source code of the Poker Buddy. This is the hardware interace for the All In Analytics project. This interface provides users with a small IoT device that allows them to track poker hands in real-time. It uses RFID based cards that generate unique signatures and allows users to store hands in the All In Analytics Core system.

## Technology Used

We will be developing on the ESP32-WROOM microcontroller. This controller will act as a small device owned by each player. This device will communicate with the dealer poker buddy (which will host the entire application). Some of the technology we will be using are down below:

### Software

- [C](https://en.wikipedia.org/wiki/C_(programming_language))
    - Primary programming language for programming the microcontroller.
- [Zephyr RTOS](https://www.zephyrproject.org/)
    - This is the RTOS flashed to the board to allow us to handle multiple tasks simultaneously with precise timing / scheduling constraints.
- [MQTT](https://mqtt.org/)
    - Also know as Message Queuing Telemetry Transport, is a lightweight messaging protocol designed for efficient communication between devices, especially in IoT (Internet of Things) applications. It operates over TCP/IP and follows a publish-subscribe model rather than the traditional request-response model. This will allow the poer buddies to communicate over a network hosted by the Raspberry-PI
- [SPI](https://en.wikipedia.org/wiki/Serial_Peripheral_Interface)
    - SPI is a high-speed, synchronous serial communication protocol used to connect microcontrollers with peripherals like sensors, displays, and memory modules. It uses a master-slave architecture and operates in full-duplex mode (data can be sent and received simultaneously). This will allow us to communicate with RFID / NFC readers.
- [GPIO](https://en.wikipedia.org/wiki/General-purpose_input/output)
    - GPIO refers to pins on a microcontroller that can be configured as either inputs or outputs for basic digital control. This will allow us to interface with simple peripherials like LED's and buttons.

### Hardware

- [ESP32 Microcontroller](https://www.espressif.com/en/products/modules/esp32)
    - The ESP32 is a powerful, low-cost microcontroller developed by Espressif Systems, designed for Wi-Fi and Bluetooth-enabled embedded applications. It’s widely used in IoT, robotics, and real-time systems due to its high performance, low power consumption, and rich set of peripherals.
- [RFID / NFC Readers](https://en.wikipedia.org/wiki/Near-field_communication)
    - RFID and NFC are wireless communication technologies used for identification, authentication, and data transfer using radio waves. They are widely used in applications like access control, payments, inventory tracking, and embedded systems.


## Environment Setup

To setup the environment please ensure you are running a linux operating system (Ubuntu 22.04.5) as this setup has only been established there. We are open to other environments being explored such as:

- [Docker on Windows](https://github.com/thistletech/esp32-devenvs)
- [WSL](https://www.instructables.com/ESP32-Development-on-Windows-Subsystem-for-Linux/)

Overall, when entering the world of embedded development it is nice to be able to create very simple and easy to understand projects. These will live directly in the `/sample` directoriy at the root of the repository. The will be explored in the second section in environment setup.

### Initializing Development Environment

#### Basic Tooling Setup

Please ensure that you have the minimum versions of the required basic tooling specifed below:

- [CMake](https://cmake.org/): v3.20.5
- [Python](https://www.python.org/): v3.10
- [Devicetree Compiler](https://www.devicetree.org/): v1.4.6

Please run the following commands below:

```bash
# Setup APT Repository
wget https://apt.kitware.com/kitware-archive.sh
sudo bash kitware-archive.sh
```

```bash
# Use APT to install all necessary dependencies

sudo apt install --no-install-recommends git cmake ninja-build gperf \
  ccache dfu-util device-tree-compiler wget \
  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
  make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1
```

```bash
# Verify versions of installed packages
cmake --version
python3 --version
dtc --version
```

#### Setup Zephyr RTOS

```bash
# Install, setup, and activate  new python virtual environment
sudo apt install python3-venv 

python3 -m venv ~/<path-to-aia-poker-buddy>/.venv

source ~/<path-to-aia-poker-buddy>/.venv/bin/activate
```

```bash
# Install west tool (used heavily in zephyr development)
pip install west
```

```bash
# Get Zephyr source code
west init ~/<path-to-aia-poker-buddy>
cd ~/<path-to-aia-poker-buddy>
west update
```

```bash
# Export a Zephyr CMake package. This allows Cmake to automatically load boilerplat code required for building Zephyr applications
west zephyr-export
```

```bash
# Install west python dependencies
west packages pip --install
```

```bash
# Install Zephyr SDK
cd ~/<path-to-aia-poker-buddy>/zephyr
west sdk install
```

### Running Samples

#### Button Sample

The button sample (lives in `/sample/button`) connects to the builtin button onboard the ESP32-WROOM platform. It has basic code associated with interacting with a simple button over GPIO pins. The instructions to build / load / run this example are below:

##### Build Application

```bash
cd ~/<path-to-aia-poker-buddy>
west build -p always -b esp32_devkitc_wroom/esp32/procpu sample/button/
```

##### Flash Application onto Device

```bash
west flash
```

##### Monitor Serial Terminal Onboard Device while Application is Running

```bash
west espressif monitor -p /dev/ttyUSB0
```

Finally, you should see the following output on your terminal:

```bash
*** Booting Zephyr OS build v4.1.0-rc2-4-g9b2fdace1d4c ***
Set up button at gpio@3ff44000 pin 0
Press the button
```

Once the user pressed the button, we can finally see output on the console as well:

```bash
Button pressed at 1596767559
Button pressed at 1597392561
Button pressed at 1598017545
Button pressed at 1598642538
Button pressed at 1599267540
Button pressed at 1599892524
Button pressed at 1600517526
Button pressed at 1601142510
Button pressed at 1601767503
Button pressed at 1602392514
Button pressed at 1603017507
```

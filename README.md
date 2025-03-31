# OpenPickleballMachine

# Open-Source Pickleball Throwing Machine

This project presents a cost-effective, open-source pickleball throwing machine designed to bridge the gap between affordability and advanced features. It provides adjustable speed, spin control, and reliable ball feeding, allowing players to practice and improve independently. The open-source design encourages community collaboration, customization, and innovation, empowering users to enhance the machine to suit their specific needs.

## Features
![image](https://github.com/user-attachments/assets/15883aba-5325-482b-960e-9b60191c7664)
- **Adjustable Speed:** Customize the speed of ball throws for a range of practice scenarios.
- **Spin Control:** Fine-tune the spin for more realistic training.
- **Reliable Ball Feeding:** Ensures consistent ball delivery for continuous practice.
- **Open-Source:** Full access to the design and code, allowing for easy modification and improvements.
- **Easy Maintenance:** Built using off-the-shelf parts, making repairs and upgrades simple.

## Requirements
BOM.XLSX for materials sourcing.
[image](https://github.com/user-attachments/assets/6ef78148-6823-4c8e-af42-3e798bc7d5f1)
# Tools required for assembly: 
3D Printer (That can print ABS/ASA), impact driver, power drill, soldering iron, crimpers, wire strippers, hot glue gun, hex driver set, jigsaw.


# 3D printing specs:
We suggest using either ASA/ABS or better as a filament. Suggested standard 3 wall, 25% gyroid/cubic infil, 0.6mm nozzle.
Rollers should be printed with 95A TPU.
#Our specs: ASA-CF 
## Installation

### 1. Hardware Setup
- Assemble the machine using off-the-shelf components, including motors, motor controllers, and the Raspberry Pi.
- Follow the wiring instructions provided in the `docs/assembly` folder to connect the components.

### 2. Software Setup
- Download balena etcher: https://etcher.balena.io/
- Using balena etcher, flash the iso file in `docs/firmware` folder to download the needed firmware.
# Connect the Pi to Wifi:
1. Open the SD card on your computer (it should be mounted as the boot partition).
2. In the root directory, create a file named `wpa_supplicant.conf` and add the following content:

   ```txt
   country=CA
   ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
   update_config=1
   network={
       ssid="YOUR_SSID"
       psk="YOUR_PASSWORD"
   }

###Clone the repository:
  ```bash
  git clone https://github.com/jc2889/OpenPickleballMachine.git
  cd OpenPickleballMachine

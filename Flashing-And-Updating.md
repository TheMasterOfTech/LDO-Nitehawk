# LDO Nitehawk Flashing Instructions

## If needing to flash Katapult:
1. In this section we will compile the Katapult Bootloader. Note that your Nitehawk toolboard normally ships with Katapult pre-installed and you only need to perform the following operations if Katapult was inadvertently overwritten or lost. 
2. Log on to your Klipper host (your Raspberry Pi) via SSH, windows users can use putty or any other SSH client. Mac and Linux users can simply connect with the ssh command in their command line terminal. Download Katapult by entering ``git clone https://github.com/Arksine/katapult``. **_If you already have Katapult on your Nitehawk, please ignore and proceed to step 3._**
3. Run the following:
   ```
   cd katapult
   make menuconfig
   ```
5. Configure Katapult to be flashed with these settings:

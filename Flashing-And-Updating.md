# LDO Nitehawk Flashing Instructions

## Flashing Katapult (only do if necessary)
### _Compiling Katapult_
1. In this section we will compile the Katapult Bootloader. Note that your Nitehawk toolboard normally ships with Katapult pre-installed and you only need to perform the following operations if Katapult was inadvertently overwritten or lost. 
2. Log on to your Klipper host (your Raspberry Pi) via SSH, windows users can use putty or any other SSH client. Mac and Linux users can simply connect with the ssh command in their command line terminal. Download Katapult by entering `git clone https://github.com/Arksine/katapult`. **_If you already have Katapult on your Nitehawk, please ignore and proceed to step 3._**
3. Run the following:
   ```
   cd katapult
   make menuconfig
   ```
4. Configure Katapult to be flashed with these settings:
<img src="https://github.com/TheMasterOfTech/LDO-Nitehawk/blob/main/katapult_make_settings.png" width="500" height="300" />

5. Enter `Q` to quit and confirm with `Yes` when prompted to save.
6. Run the following commands to compile and generate the Katapult binary files:
   ```
   make clean
   make
   ```
7. A binary file called `katapult.uf2` will now be created in the location `~/katapult/out/`. Katapult is now compiled ready to be flashed.

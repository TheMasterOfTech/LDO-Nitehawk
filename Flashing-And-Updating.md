# LDO Nitehawk Flashing Instructions
_Generated based on the official documenation as listed by LDO, with corrections to the previous/current documentation._

## Installing Klipper (MANDATORY)
The following instructions are for compiling and flashing new Klipper firmware to your Nitehawk toolboard. You need to perform these steps if you want to update your Klipper firmware to the newest version or if you are doing a fresh install and your Nitehawk only currently possesses Katapult at this time. Before compiling the firmware, you will need to have Klipper already installed on your host device (e.g. Raspberry Pi).

### _Compiling Klipper_
1. Login to the Raspberry Pi via SSH and run the following commands to open the firmware configuration interface:
   ```
   cd ~/klipper
   make menuconfig
   ```
2. In the configurator, select `Enable extra low-level configuration options`, choose `Raspberry Pi RP2040` and ensure all other settings match the rest of the settings depicted within the screenshot below:
<img src="https://github.com/TheMasterOfTech/LDO-Nitehawk/blob/main/klipper_make_settings.png" width="500" height="300" />
   - **⚠️ Most importantly, make sure you set the `16KiB  bootloader offset`. Otherwise, you will erase the Katapult bootloader!**
4. Enter `Q` to quit and confirm with `Yes` when prompted to save.
5. Run the following commands to generate the firmware file:
   ```
   make clean
   make
   ```
6. A firmware file called `klipper.bin` will now be generated and can be located in the directory `~/klipper/out`. You are now ready to upload the Klipper firmware to the Nitehawk toolboard. Follow the directions below to accomplish flashing Klipper.

### _Flashing Klipper_
1. Ensure the Nitehawk is in the Katapult bootloader mode (the `ACT` light should blink slowly when in this mode). If you need to enter the Katapult bootloader mode, enter it by **quickly double pressing** the ``RESET`` button on the toolboard.
2. Run `ls /dev/serial/by-id` to find the USB ID of your Nitehawk toolboard. You should see something like: `usb-katapult_rp2040_A1234567898D1234-if00` which is the USB serial address of Nitehawk currently running the Katapult bootloader.
   - ⚠️ **This is crucial, you need it to be in Bootloader mode before flashing Klipper! Think of it as when installing a Windows OS image for the first time on a brand new PC.**
3. Run `pip install pyserial`. _This will install the pyserial python module if it is not present._
4. Run the following commands to upload the Klipper firmware to the Nitehawk MCU directly:
   ```
   cd ~/klipper
   sudo service klipper stop
   make flash FLASH_DEVICE=/dev/serial/by-id/usb-katapult_rp2040_<your_USB_ID_suffix_from_step_2_here>
   sudo service klipper start
   ```
5. If you encounter any connection issues after flashing the new firmware, ensure your Nitehawk has exited out of Katapult bootloader mode by running the `ls /dev/serial/by-id` command to verify. If you see something like `usb-katapult_rp2040_A1234567898D1234-if00`, your Nitehawk is still in Katapult bootloader mode, however, if you see something like `usb-Klipper_rp2040_E6626005A71FA435-if00`, your Nitehawk is already out of Bootloader mode and in Klipper mode. Proceed to perform a firmware restart.
6. Your Nitehawk should now have updated to the latest version of Klipper! To confirm, sign into your printer's management portal (Mainsail, Fluidd, etc.) and navigate to the area where it lists the Nitehawk's currently installed Klipper version. If it matches your host's Klipper version, you're done! Congratulations!

## Installing Katapult (NECESSITY ONLY)
In this section we will compile the Katapult Bootloader. Note that your Nitehawk toolboard normally ships with Katapult pre-installed and **you only need to perform the following operations if Katapult was inadvertently overwritten or lost**. 

### _Compiling Katapult_
1. Log on to your Klipper host (your Raspberry Pi) via SSH, windows users can use putty or any other SSH client. Mac and Linux users can simply connect with the ssh command in their command line terminal. Download the latest version of Katapult by entering `git clone https://github.com/Arksine/katapult`.
2. Run the following:
   ```
   cd katapult
   make menuconfig
   ```
3. Configure Katapult to be flashed with these settings:
<img src="https://github.com/TheMasterOfTech/LDO-Nitehawk/blob/main/katapult_make_settings.png" width="500" height="300" />

4. Enter `Q` to quit and confirm with `Yes` when prompted to save.
5. Run the following commands to compile and generate the Katapult binary files:
   ```
   make clean
   make
   ```
6. A binary file called `katapult.uf2` will now be created in the location `~/klipper/katapult/out/`. Katapult is now compiled ready to be flashed.

### _Flashing Katapult_
1. Boot the Nitehawk toolboard into system boot mode by following the below substeps:
   1. Press and hold both the `RESET` and `BOOT0` button.
   2. Release the `RESET` button, followed by then the `BOOT0` button.
   3. If done correctly, your Nitehawk should now have entered system boot mode and become a sort of semi “thumbdrive”. Run the command `ls /dev/sda*` to confirm. You should see something like `/dev/sda/ dev/sda1`.
         - _If you get something like `ls: cannot access '/dev/sda*': No such file or directory` this means either Nitehawk didn't enter boot mode or there is a problem with the physical connection between the Raspberry Pi and Nitehawk._
2. Start the flashing process with the following command(s):
   ```
   sudo mkdir -p /mnt/pico
   sudo mount /dev/sda1 /mnt/pico
   sudo cp ~/klipper/katapult/out/katapult.uf2 /mnt/pico
   sudo sync
   sudo umount /mnt/pico
   ```
3. The previous step essentially mounts the Nitehawk as a storage drive and copies the katapult binary files into that drive. It then unmounts the drive. If everything went smoothly, you should now be able to see the ACT light located below the `BOOT0` button blink slowly.
   - To double check that Katapult is installed, run `ls /dev/serial/by-id`. You should see something like: `usb-katapult_rp2040_A1234567898D1234-if00` which is the USB serial address of Nitehawk running Katapult. A few small details to note here:
     1. You will only see this address when Nitehawk is in Katapult bootloader mode and not in system boot mode or when Nitehawk is normally running Klipper.
     2. You can force Nitehawk to enter the Katapult bootloader by **quickly double pressing** the ``RESET`` button on the toolboard. The `ACT` light blinks slowly in this mode as previously mentioned.
     3. You will need to enter the Nitehawk into the Katapult bootloader mode and record the Katapult USB serial address to upload Klipper.
     4. By following the above instructions, you will have uploaded Katapult, but erased all other firmware (including any previously installed Klipper firmware).
     5. Exit the Katapult bootloader by **single pressing** the `RESET` button, this would then normally have the Nitehawk enter Klipper firmware, however, since you just erased Klipper, you will just re-enter the Katapult bootloader.
     6. You are now ready to compile and flash Klipper.

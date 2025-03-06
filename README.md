# PX4 Setup and Flashing Guide for Holybro Kakute H7 Mini Flight Controller

This guide provides a detailed, step-by-step process for flashing the PX4 firmware onto the **Holybro Kakute H7 Mini** flight controller, including the setup of STM32CubeProgrammer, flashing the bootloader, and uploading the PX4 firmware.

## Prerequisites

Before starting, ensure you have:

- **Holybro Kakute H7 Mini** flight controller (https://holybro.com/products/kakute-h7-mini?variant=44722798788797)
- A **USB cable** for data transfer (not just charging)
- **QGroundControl** installed on your computer
- **PX4 Firmware** source code
- **STM32CubeProgrammer** installed
- **dfu-util** (for potential troubleshooting)

## Step 1: Put the Board in DFU Mode

1. **Prepare the Board**: Press and hold the BOOT button on the flight controller.
2. **Insert the USB Cable**: While holding the BOOT button, connect the flight controller to your PC.
3. **Verify DFU Mode**: Once the board is connected, use the terminal to check if it's in **DFU mode** by running:
   ```
   lsusb
   ```
   - Look for an entry similar to `STMicroelectronics STM Device in DFU Mode`.

   **Tip**: If you do not see the board in DFU mode, try using **dfu-util** to reflash the bootloader. Here’s a quick troubleshooting tip:
   
   - I encountered a problem where STM32CubeProgrammer did not detect the board initially. I used `dfu-util` in the terminal to try flashing the bootloader and PX4 firmware directly, but it didn't work. However, after this, the board showed up correctly in STM32CubeProgrammer.

## Step 2: Open STM32CubeProgrammer

1. **Launch STM32CubeProgrammer**: Open the STM32CubeProgrammer application.
2. **Select USB as Connection**: In STM32CubeProgrammer, at the top right, select `USB` as the connection method.
3. **Refresh the Port**: Hit the **Refresh** button next to the **Port** dropdown to find the correct USB port.
  ![1](https://github.com/user-attachments/assets/9d3d9b9d-a4df-4b08-acc6-fc570814d16a)
   
   **Tip**: If the board is still not detected (you see "No DFU Detected"), refer to the `dfu-util` troubleshooting steps mentioned earlier.

4. **Connect to the Board**: Once the correct port is selected, click **Connect** at the top right to establish the connection.
  ![2](https://github.com/user-attachments/assets/4207113c-5bde-48a1-b06a-993ca6bf6b8a)


## Step 3: Flash the Bootloader

1. **Navigate to Erasing and Programming Tab**: On the left panel of STM32CubeProgrammer, click the second tab called **"Erasing and Programming"**.
2. **Full Chip Erase**: In the middle blue panel, hit **Full Chip Erase** to clear the flash memory of the board.
![3](https://github.com/user-attachments/assets/ed6bdc9c-cc41-46ba-bf08-3650f1e15450)
3. **Select the Bootloader File**: On the left panel, find the bootloader file `holybro_kakuteh7mini_bootloader_ID_1058.hex` (attached to the repo) and select it.
![6](https://github.com/user-attachments/assets/8a931f99-2d4b-41cf-8033-b2ae8b86edc7)
4. **Start Programming**: Hit **Start Programming** to flash the bootloader to the board.
![7](https://github.com/user-attachments/assets/8efd707c-2cc7-4dba-bc1e-9c1dba241a92)
   
   **Tip**: Ensure that you are flashing the correct bootloader version for your specific board (Holybro Kakute H7 Mini).

5. **Power Cycle the Flight Controller**: After flashing the bootloader, power cycle the flight controller by unplugging and replugging it into your computer **without** holding the button (this takes it out of DFU mode).

## Step 4: Build the PX4 Firmware

1. **Clone the PX4 Repository**: Clone the PX4-Autopilot repository to get the firmware source code:
   ```
   git clone --recursive https://github.com/PX4/PX4-Autopilot.git
   ```

2. **Navigate to the PX4 Directory**: Change into the PX4-Autopilot directory:
   ```
   cd PX4-Autopilot
   ```

3. **Build the Firmware**: Build the PX4 firmware for your specific board (Holybro Kakute H7 Mini):
   ```
   make holybro_kakuteh7mini_default
   ```
   - This will generate the firmware file `holybro_kakuteh7mini_default.px4` located in the `build/holybro_kakuteh7mini_default` directory.

## Step 5: Flash the PX4 Firmware using QGroundControl

1. **Open QGroundControl**: Launch **QGroundControl** (QGC).
![9](https://github.com/user-attachments/assets/c0c631b9-8ab0-4698-9105-832bf0282cb5)
2. **Go to Firmware Update**: In the QGC interface, click on the **logo** at the top left to open the menu, and then go to **Vehicle** > **Firmware**.
3. **Reconnect the Flight Controller**: If prompted, unplug the flight controller from the USB and then reconnect it to your computer.
![10](https://github.com/user-attachments/assets/09994f98-3d5e-46e3-b317-4ab6e6854c34)
4. **Select Firmware Setup**: After reconnecting, QGroundControl will open a pop-up menu called **Firmware Setup**.
   - Click on **Advanced Settings**.
   - In the **Firmware Setup** menu, choose **Custom Firmware** from the dropdown.
5. **Select the Custom Firmware**: Navigate to the location where you built the PX4 firmware (`PX4-Autopilot/build/holybro_kakuteh7mini_default/`), and select the file `holybro_kakuteh7mini_default.px4`.
![11](https://github.com/user-attachments/assets/673d66ca-c58d-4fbf-8382-c8bd39dd4906)
6. **Flash the Firmware**: Click **OK**, and QGroundControl should now begin flashing the PX4 firmware to the flight controller.
![12](https://github.com/user-attachments/assets/9a99cf58-c244-4bd5-a796-b02b27d1164d)

## Step 6: Verify Firmware Installation

1. **Check Firmware Installation**: Once the firmware is flashed, check **QGroundControl** to ensure the flight controller is correctly detected and the firmware is successfully installed.
2. **Final Steps**: Complete any necessary configuration steps within QGroundControl to calibrate sensors and set up your vehicle.

---

## Troubleshooting Tips

- **No DFU Detected**: If the STM32CubeProgrammer does not detect the board, use `dfu-util` to force the bootloader installation via terminal. Once this is done, STM32CubeProgrammer should detect the device.
- **Check USB Cable**: Ensure you're using a data cable (not just a charging cable). Some USB cables only support charging and may not work for data transfer.
- **USB Port Issues**: If the flight controller is not detected, try switching USB ports or using a USB 2.0 port instead of a USB 3.0 port.
- **Check Permissions (Linux Only)**: If you're on Linux, make sure your user has permission to access the USB device:
   ```
   sudo usermod -aG dialout $USER
   ```
   - This ensures your user has access to the serial ports.

---

## Conclusion

You have successfully flashed **PX4 firmware** onto your **Holybro Kakute H7 Mini** flight controller! Now, you can begin configuring your drone, calibrating sensors, and preparing for flight using **QGroundControl**.

Feel free to refer back to this guide whenever you need to set up or reflash the firmware.

---

## References

- [PX4 Autopilot GitHub Repository](https://github.com/PX4/PX4-Autopilot)
- [STM32CubeProgrammer Documentation](https://www.st.com/en/development-tools/stm32cubeprog.html)
- [QGroundControl](http://qgroundcontrol.com/)

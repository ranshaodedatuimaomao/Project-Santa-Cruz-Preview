<!---
title: USB updating                        # the article title to show on the browser tab
description: Walks a user through the usb update process for the devkit carrier board (July 2020).
author: elqu20      # the author's GitHub ID - will be auto-populated if set in settings.json
ms.author: v-elqu     # the author's Microsoft alias (if applicable) - will be auto-populated if set in settings.json
ms.date: {@date}           # the date - will be auto-populated when template is first applied
ms.topic: reference  # the type of article
--->

# USB updating

This guide will show you how to flash the carrier board of the Project Santa Cruz Development Kit with a new image file over USB. Ensure all prerequisites are satisfied before working through the USB update procedure.

There are two USB update methods:

<!---- Method #1: .swu file with a USB storage device.--->
- Method #1: .raw file with the NXP UUU tool.
- Method #2: variation of method #1 for non-standard situations (i.e. your device does not boot).

Please note that both methods delete the contents of your device's data partition during the update process. If your data is deleted during the update process (i.e. has been reset to factory settings), you will need to work through the [OOBE](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/user-guides/getting_started/oobe.md) again to set up WiFi connections, SSH login information, etc.

## Prerequisites

- Windows or Linux PC with an available USB-C port.

<!----- USB storage device (for method #1).--->

- Carrier board and USB-C cable, included in the Project Santa Cruz Development Kit.  

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

- [NXP UUU tool](https://github.com/NXPmicro/mfgtools/releases). Download the **Latest Release** uuu.exe file (for Windows) or the uuu file (for Linux) under the Assets tab.

    ![nxp](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/usb_nxp.png)

- [7zip](https://www.7-zip.org/). This software will be used for extracting the raw image file from its XZ compressed file. Download the appropriate .exe file and click on the .exe file to install 7zip.  

## USB update method #1: NXP UUU tool

1. On your PC, navigate to the [Project Santa Cruz update management website](https://projectsantacruz.microsoft.com/Download). Download the full devkit image (**pe101-uefi-\<version>.raw.xz**) as well as the associated **emmc_full.txt** and **fast-hab-fw.raw** files.

    ![update_download](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/usb_update_download.png)

1. Extract the **pe101-uefi-\<version>.raw** file from the compressed **pe101-uefi\<version>.raw.xz** file. Right click on the .xz image file and select **7-Zip** > **Extract Here**.  

    ![extract_update_files](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/usb_extract_update_files.png)

1. Copy the extracted **pe101-uefi-\<version>.raw** file, as well as the associated **emmc_full.txt** and **fast-hab-fw.raw** files, to the folder containing the UUU tool (uuu.exe for Windows or uuu for Linux).  

1. Plug in the carrier board power cable and turn on the device.  

1. Connect to the devkit's Wi-Fi AP (password = santacruz).

1. Open PuTTY and [SSH into your device](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/user-guides/general/troubleshooting/ssh_and_serial_connection_setup.md#ssh-into-the-devkit).

    ![putty](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/ota_putty.png)

1. Next, open a Windows command prompt (**Start** > **cmd**) or a Linux terminal and navigate to the folder where the update files are stored. Run the following command:

    - Windows:
        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version>.raw  
        ```
    - Linux:
        ```console
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version>.raw  
        ```

    ![cmd_flash](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/usb_cmd_flash.png)  

1. Connect the carrier board to the host PC with the USB-C cable.  

1. In the PuTTY terminal, run the following commands:

    1. Set the device to usb update mode.

        ```console
        flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Reboot the device. The update installation will begin.

        ```console
        reboot -f
        ```

        ![putty_usb_update_mode](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/usb_putty_usb_update_mode.png)

1. Navigate back to the command prompt. When the update is finished, you will see the following screen:

    ![update_complete](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/usb_update_complete.png)
  
1. Once the update is complete, power off the carrier board. Unplug the USB-C cable from the PC.  

1. Power the carrier board back on.

1. To verify the update, SSH into your devkit to check the software version.

    1. First, reconnect to the devkit's Wi-Fi AP (password = santacruz).

    1. Restart the PuTTY session and enter the following:

        ```console
        cat /etc/adu-version
        ```

        The terminal will display the current software version, which should match the installed update (**pe101-uefi-\<version>.raw**).

        ![putty_terminal](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/images/ota_putty_terminal.png)

## USB update method #2: non-standard situations

There are a few situations where it is not possible to gracefully USB update (re-flash) the carrier boards (i.e. if you need to recover an unbootable device). In these situations, please follow this guidance.

 1. Toggle the Boot Configuration DIP switches to 1011 and remove the SD card so the device will boot into USB flash mode.

 1. Run the UUU command corresponding to your build (see method #1 above).

 1. Power on the device.

 1. Wait for UUU to complete, then power down the carrier board.

 1. Toggle the DIP switches to eMMC boot mode (1001).

## Provide feedback

After completing the USB update experience as well as the [OTA OS update experience](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/ota_update.md) and [OTA firmware update experience (Eye/Ear SoM)](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/updating/ear_som_firmware.md), please provide feedback on your experience via this [questionnaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-EYOjUzOMlKvDaulVXd95tUNDc1V05EMDA2NjBRVDc5UlZBMVkwRjRNQSQlQCN0PWcu). Your feedback will help us continue to fine-tune and improve the update experiences.

For more information on Project Santa Cruz Quests and to provide feedback on other experiences, please visit the [test scenarios page](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/user-guides/general/test-scenarios.md).

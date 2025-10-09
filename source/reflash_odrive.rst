**Reflashing Odrives**
========================================================================================================================
(If motors do not go into closed loop and Odrive will not connect, reflashing may be a temp fix)

1. Install STM32CubeProgrammer software `here <https://www.st.com/en/development-tools/stm32cubeprog.html>`__

2. Download appropriate firmware version `here <https://docs.odriverobotics.com/releases/firmware>`__ 
      (Current version used is 0.5.5, firmware file found
      `here <https://github.com/RAMBotsCSU/testing/tree/main/o-drive>`__,
      can check current version on an odrive by following instructions
      in How-To Check the Firmware Version)

3. Flip switch on Odrive to DFU (left switch on ODESCS, not labeled),
      and power cycle Odrive.

4. Follow directions under "Alternative DFU Tool" section of Odrive docs
      `here <https://docs.odriverobotics.com/v/0.6.2/dfu.html#alternative-dfu-tool>`__

-  BE SURE TO CONNECT TO USB BEFORE OPENING FILE OR SOFTWARE WILL
      CRASH

-  IF NO PROGRESS IS BEING MADE RESTART SOFTWARE AND IT SHOULD WORK

-  IF YOU GET AN ERROR ABOUT "NO DFU DEVICE FOUND" MAKE SURE THE
      ODRIVE IS IN DFU MODE (SWITCH TO LEFT) AND POWER CYCLED

-  *Helpful guide if needed*
      `here <https://web.archive.org/web/20211125225055/https://docs.odriverobotics.com/odrivetool#upgrading-firmware-with-a-different-dfu-tool>`__

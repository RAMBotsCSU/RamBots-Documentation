**Booting Up the RAMBot**
========================

Follow these steps to power up the RAMBot safely and start the onboard startup script.

Power check
-----------
- For battery power: use the cell checker and ensure the pack is above 22.1 V (≈ 3.7 V per cell) before connecting.
- For bench supply: verify the supply is set to the correct voltage and current limit.

Startup steps
-------------
1. Power the RamBOT using the switch on the back of the robot.
2. Wait for the Raspberry Pi to boot fully.
3. On the Pi desktop, locate the script named `startup.sh` on the desktop.
4. Run the script by double-clicking and selecting "Run in Terminal" (or run it manually from a terminal):

   .. code-block:: console

      ./Desktop/startup.sh

.. note::
   If the script requires elevated permissions, run it with the appropriate user or sudo as needed.

Troubleshooting
---------------
- If the Pi does not boot, check power, SD card integrity, and display/keyboard output for errors.
- If the robot does not power up, re-check battery voltage or bench supply settings.

.. warning::
   Do not connect batteries or apply power if cell voltages are below the safe threshold — this can damage the battery pack.
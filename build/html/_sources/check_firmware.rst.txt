How to Check Firmware Version
=============================

This short guide shows how to read the ODrive firmware version using odrivetool.

Prerequisites
-------------
- Install odrivetool (installation instructions: `ODrive docs <https://docs.odriverobotics.com/v/latest/interfaces/odrivetool.html#installation>`__).
- Connect the ODrive to your computer.

Procedure
---------
1. Start odrivetool (run `odrivetool` on the command line).
2. In the odrivetool Python prompt, read the firmware fields for the connected device (typically `odrv0`):

   .. code-block:: console

      odrv0.fw_version_major
      odrv0.fw_version_minor
      odrv0.fw_version_revision

3. Combine the values as major.minor.revision (for example: 0.5.6).

.. note::
   If your device is not named `odrv0` in the session, replace `odrv0` with the appropriate variable shown when odrivetool connects.

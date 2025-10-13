**How to Check Firmware Version**
=====================================================================================================================
*Useful for when you need to know the version for reflashing*

1. Open odrivetool on your computer (instructions for install found `here <https://docs.odriverobotics.com/v/latest/interfaces/odrivetool.html#installation>`__)

2. Connect your computer to the odrive in question

3. Run these commands one after another:

   a. <odrive name>.fw_version_major

   b. <odrive name>.fw_version_minor

   c. <odrive name>.fw_version_revision

4. That's the version! It's major.minor.revision e.g. if major = 0,
      minor = 5, and revision = 6, the version is 0.5.6

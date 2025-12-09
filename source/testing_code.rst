Testing Repository
==================

This page documents the testing repository used for diagnostics, test code, and archived development artifacts for the RamBOT project.

Repository layout
-----------------
* LoadCell/Leg-Strength-Testing/ — ESP32 code for real-time load-cell data (2024–2025).
* archive/ — Older code and scripts (2022–2023).
* lidar/ — Raspberry Pi + LiDAR test scripts (2023–2024).
* o-drive/ — Calibration and test scripts for ODrive (various years).
* pi/ — Pi-related scripts: sound, startup testing, archived deployed code (2022–2023).
* plotter/ — matplotlib tools to plot ODrive current data in real time (2024–2025).
* single-leg/ — Legacy Python code for the testbench (2023–2024).
* teensy/ — Legacy Teensy test scripts (2023–2024).

LoadCell/Leg-Strength-Testing
-----------------------------
PlatformIO code for an ESP32 that uses an HX711 load cell amplifier. The ESP32 serves a local webpage that plots real-time force data. Connect to the ESP32 access point to view the page.

archive
-------
Collection of older scripts and projects, including:

* o-drive/SinWave_Test/ — Arduino sketch used to move ODrive in simple tests.
* pi/ — Iterative Pi scripts testing threading, controller connectivity, and GUI experiments.
* teensy/ — Older Teensy scripts for gyroscope filtering and ESC tests.

lidar
-----
Python test scripts for the Adafruit LiDAR sensor. Some scripts were deployed on the Raspberry Pi; functionality varies by script and year.

o-drive
-------
Calibration and support files for ODrive boards, e.g.:

* ConfigFile.py — Calibration utility using odrivetool.
* Single-Motor-cfg.py — Single-motor calibration helper.
* firmware_5_5.elf — Firmware image (v5.5) referenced by older deployments.
* OdriveArduino.zip — Unofficial Arduino helper library (archived).

pi
--
Scripts related to Raspberry Pi development and testing:

* Sounds — Library of audio files used by legacy code.
* Startup Testing — Scripts that validate ODrive calibration and startup behavior.
* pi_edays_demo — Deployed demo code (E-Days 2023).

plotter
-------
* odrive-plotter.py — Matplotlib-based real-time plotting of ODrive currents.

single-leg
----------
Legacy single-leg simulation and testbench scripts:

* com_sim.py / com_sim_linux.py — Kinematic simulation and Linux port.

teensy
------
Older Teensy-based calibration and test scripts:

* manual_offset_calibration — Tools for determining leg offsets.
* single_leg_stand — Testbench code for single-leg standing.
* assorted deprecated demos and utilities (archived).

.. note::
   This page is an overview. Consult individual directories for README files and specific usage instructions.
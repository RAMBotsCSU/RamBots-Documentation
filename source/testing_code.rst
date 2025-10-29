Testing Repository
==================

The testing repository contains various test scripts, archived code, and resources for troubleshooting and developing on the RamBOT

File Structure
------------------------
- LoadCell/Leg-Strength-Testing/: Code deployed on the ESP32 that shows real-time loadcell data. (2024-2025)
- archive - old code and test scripts (2022-2023)
- lidar - test code for Raspberry Pi and LiDAR sensor (2023-2024)
- o-drive - all calibration and test scripts for the o-drive as well as firmware and libraries (various years)
- pi - sound code, startup testing, archived deployed pi code (2022-2023)
- plotter - matplotlib code to plot odrive current data in real-time (2024-2025)
- single-leg - old python code to run the testbench (2023-2024)
- teensy - old teensy test scripts for calibrating offsets (2023-2024)


Subsections
------------------------

LoadCell/Leg-Strength-Testing/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
PlatformIO code that runs on an ESP32 with an HX711 and Load cell sensor. The ESP32 broadcasts a webpage that plots real-time force on a local webpage. Must connect to broadcasted access point first.

archive
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- o-drive/SinWave_Test/: simple arduino script that reads and moves o-drive
- pi - Several iterative pi scripts to test threading, playstation controller connection, different GUI libraries, pygame, and archived deployed code (2022-2023)
- teensy - old teensy test scripts: kalman gyroscopic filtering, several gyroscope test scripts (MPU6050), esc test script, and arcived deployed teensy code (2022-2023)

lidar
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Several test scripts for the Adafruit LiDAR sensor written in python. Some were deployed on pi, but all in varying degrees of functionality. (2023-2024)


o-drive
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ConfigFile.py - calibration script that uses odrivetool to calibrate two o-drive motors.
EditSettings - depreciated
OdriveArduino.zip - arduino library for o-drive, unnofficial, but used in old deployed code.
Single-Motor-cfg.py - calibration script that only calibrates a single motor on an o-drive.
firmware_5_5.elf - o-drive firmware version 5.5, currently deployed on all o-drives.
test.py - depreciated

pi
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- Sounds - library of sound files used in old code
- Startup Testing - test script that reads odrive values to validate proper calibration
- pi_edays_demo - old deployed code for E-Days 2023

plotter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- odrive-plotter.py - matplotlib code that shows current readings in real-time 

single-leg
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- com_sim.py - old python code for simulating single leg movements (2023-2024)
- com_sim_linux.py - ported version of com_sim.py to linux

teensy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- manual_offset_calibration - old code for determining offsets for legs (2023-2024)
- single_leg_stand - old code for running testbench leg (2023-2024)
- standalone_kinematics - unknown, depreciated
- teensy_edays_demo - unknown, depreciated
- teensy_edays_demo_pre_sit - unknown, depreciated
- teensy_serial_main_opendog - unknown, depreciated
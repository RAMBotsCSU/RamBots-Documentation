How to Recalibrate Motors
=========================

Prerequisites
-------------

1. If you have not downloaded odrivetool yet, download and install it `here <https://docs.odriverobotics.com/v/latest/interfaces/odrivetool.html#installation>`__
2. Make sure you have `Visual Studio Code <https://code.visualstudio.com/>`__ and our `motion repository downloaded <https://github.com/RAMBotsCSU/motion>`__
3. For the best results in calibration, the motors should be calibrated
   with no load.

   1. Take apart the leg to make sure both motors on the ODESC can spin
      freely, BUT don't take off the encoder plate from the back of
      either
   2. Make sure both motors are facing up with the encoder plate down on
      a flat and level surface
   3. Note: You *can* run calibration with the legs assembled; however,
      there may be stability issues, so run calibration at your own
      risk.

Running the Script
------------------

4. Connect the ODESC to your computer via its micro USB port
5. Power on the ODESC, either by bench supply or by batteries
6. Run the calibration tool

   1. The script to run can be found under
      motion/tools/odrive/`Calibrate.py <http://Calibrate.py>`__
   2. Note: We have set up Pipfile to make it easier to run the script
      with the necessary dependencies, but you'll need `pip <https://pip.pypa.io/en/stable/installation/>`__ and `pipfile <https://pipenv.pypa.io/en/latest/pipfile.html>`__ setup
      (*guide to be written later*)

7. If all has been set up correctly:
   1. Each motor will make a beeping noise
   2. Each move will move a short distance in each direction
      1. MAKE SURE MOTOR CAN MOVE UNIMPEDED, if it can't, the
         calibration will fail.

8. After each motor has finished being calibrated, disconnect the
   computer from the ODESC and reassemble the leg

How to Recalibrate Motors
=========================

Prerequisites
-------------

- Install odrivetool (instructions `here <https://docs.odriverobotics.com/v/latest/interfaces/odrivetool.html#installation>`__).
- Install Visual Studio Code: `Download Visual Studio code <https://code.visualstudio.com/download>`__.
- Clone the motion repository `here <https://github.com/RAMBotsCSU/motion>`__.
- For best results, calibrate motors with no mechanical load.

Preparation
-----------

1. `Disassemble the leg <https://rambots-documentation.readthedocs.io/en/latest/leg_disassembly.html>`__ enough so both motors on the ODESC can spin freely.
   - Do NOT remove the encoder plate from the back of either motor.

.. note::
   Place both motors face-up (encoder plate down) on a flat, level surface during calibration.

2. Optionally: You may run calibration with legs assembled, but stability issues can occur — run at your own risk.

Running the Script
------------------

1. Connect the ODESC to your computer via micro USB.
2. Power the ODESC (bench supply or batteries).
3. Run the calibration script:

   - Script path: motion/tools/odrive/Calibrate.py
   
   - Example using pipenv:

     .. code-block:: console

        pipenv run python motion/tools/odrive/Calibrate.py

   - Example without pipenv (ensure dependencies installed):

     .. code-block:: console

        python motion/tools/odrive/Calibrate.py

.. note::
   A Pipfile is provided to simplify dependency management; install with pipenv if available.

What to expect
--------------

- Each motor will emit audible beeps during calibration.
- Motors will move short distances in both directions.
- MAKE SURE each motor can move unimpeded; calibration will fail if movement is obstructed.

After Calibration
-----------------

1. When calibration completes, disconnect the computer from the ODESC.
2. Reassemble the leg.

.. warning::
   If you remove the encoder plate at any time, the encoders will need to be reinitialized. Only remove it if you intend to perform reinitialization.

Common ODrive Errors
====================

This page summarizes common ODrive error categories and what they typically indicate.

Encoder Errors
--------------
- EncoderError.ABS_SPI_COM_FAIL
  - Description: Encoder not communicating properly with the ODrive. Usually caused by a wiring fault or poor connection on the encoder SPI lines.
  - Actions: Check encoder cable continuity and shielding, verify connectors, and reseat/remove any damaged wiring.

Motor Errors
------------
- MotorError.DRV_FAULT
  - Description: Motor driver chip signaled a fault (DRV fault line triggered). The driver may still report back, but a fault was detected.
  - Actions: Inspect driver board for overheating, check motor wiring, verify motor driver power rails.

- MotorError.UNKNOWN_VOLTAGE_COMMAND
  - Description: Invalid motor configuration (for example incorrect torque_constant or pole pairs) or invalid command during calibration.
  - Actions: Verify motor parameters (torque_constant, pole_pairs) and re-run motor calibration.

Controller Errors
-----------------
- ControllerError.SPINOUT_DETECTED
  - Description: Mechanical and electrical power disagree (the controller detects unexpected motor/encoder movement).
  - Actions: Check encoder offset calibration, ensure encoder is not slipping on the motor shaft, verify encoder cable shielding and connections.

- ControllerError.INVALID_ESTIMATE
  - Description: State estimator produced an invalid estimate (may not occur with newer firmware).
  - Actions: Check firmware version and consider updating or reflashing if errors persist.

.. note::
   These descriptions are general guidance. For low-level debugging, consult the ODrive firmware documentation and use odrivetool to inspect the device status and error registers.

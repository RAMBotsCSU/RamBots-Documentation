Common O-Drive Errors
=====================

Encoder Errors
--------------

+-----------------------------------+-----------------------------------+
| EncoderError.ABS_SPI_COM_FAIL     | Encoder not communicating         |
|                                   | properly with the o-drive, most   |
|                                   | likely a fault in the wire        |
|                                   | connections.                      |
+===================================+===================================+
+-----------------------------------+-----------------------------------+

Motor Errors
------------

+------------------------------------+-------------------------------------+
|| MotorError.DRV_FAULT DRV fault:   || ODrive's motor driver chip         |
|| none                              || *signaled a fault line* at some    |
||                                   || point, but is reporting back fine  |
||                                   || as of now. Check other errors first|
+====================================+=====================================+
|| M                                 || Check your                         |
|| otorError.UNKNOWN_VOLTAGE_COMMAND || motor.config.torque_constant and   |
||                                   || your pole-pairs. Usually, this     |
||                                   || error is caused by invalid motor   |
||                                   || parameters or invalid motor        |
||                                   || calibration                        |
+------------------------------------+-------------------------------------+

Controller Errors
-----------------

+-----------------------------------+-----------------------------------+
| ControllerError.SPINOUT_DETECTED  | The motor's mechanical power and  |
|                                   | electrical power do not agree.    |
|                                   | Check your encoder offset         |
|                                   | calibration Check that your       |
|                                   | encoder is not slipping on the    |
|                                   | motor. Check your cable shielding |
|                                   | on the encoder wire               |
+===================================+===================================+
| ControllerError.INVALID_ESTIMATE  | Not actually supposed to exist    |
|                                   | using the current firmware, so it |
|                                   | might be time to check your       |
|                                   | firmware version and possibly     |
|                                   | reflash                           |
+-----------------------------------+-----------------------------------+

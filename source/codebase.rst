Motion Repository
================

High-Level Code Overview
------------------------

Provides a general overview of each code file, what it does on a high level 

Axis.cpp
~~~~~~~~
    Contains methods for initializing axis, fetching coordinates, and resetting ODrives. Contains method for sending position to ODrives.
    .. dropdown:: axis.cpp
      .. tabs:: 
        .. tab:: 
          .. code-block:: 
            Axis::Axis(Odrive& _odrive, int _id)
            Sets ODrive and id of axis to specified parameters (there are 2 Odrives per axis)


kinematics.cpp
~~~~~~~~~~~~~~
Contains method for inverse kinematics calculations, which takes
position (x, y, z, roll, pitch, yaw), and translates them to relative
ODrive movements for each joint. Contains methods for walking, pushups,
and dancing.

leg.cpp
~~~~~~~
Contains helper method for movement of all joints in a leg.

log.cpp
~~~~~~~
Contains helper method for logging errors/information while DEBUG flag
is set. Running in debug mode slows serial communications for the rest
of the robot.

main.cpp
~~~~~~~~
Initializes serial communications of the robot, then calls setup method
in sparky.cpp. Contains loop code, which calls update method in
sparky.cpp.

odrive.cpp
~~~~~~~~~~
Contains methods for initializing and connecting to an ODrive.
Initializes axis for each ODrive. Defines parameters for ODrive setup.

sparky.cpp
~~~~~~~~~~
Contains setup method, which initializes all serial communications on
the robot. Contains update method, which is called in the main.cpp loop.
Update checks tick time so that the robot is updated every 10ms, then
checks which mode (walk, pushup, dance) the robot is in. IMU data is
sent to the respective kinematics function. The IMU is then updated with
current values. Updates to internal states are then made (button
presses, operation mode).

utils.cpp
~~~~~~~~~
Contains methods for trimming stick positions (controller deadzone) and
filtering motions.

High Level Summary
~~~~~~~~~~~~~~~~~~
Upon running setup in main.cpp, the following happen: 
* A serial monitor is set up 
* sparky.setup() is called 
* In sparky.setup(), serial connections are created for all 6 of the ODrives. The IMU is then initialized, and all ODrive connections are made

The majority of functionality occurs in sparky.update(), which is called
from main.loop(). The following happen: 
* The current time is fetched. If it has been < 10ms since the last update, sparky does not update 
* Ensures controller and ODrives are connected 
* Checks current mode, then calls the respective method in kinematics.cpp (walk, pushUp, or dance)
* Checks the IMU for current roll/pitch, and stores them in class variables 
* Checks for updates to controller state, and stores button values in class variables 
* Checks for ODrive errors


Low-Level Code Overview
------------------------


.. code-block:: none

   • Axis::init()
     - Checks for errors then tries to reset them by calling

.. code-block:: none

   • Axis::reset()
     - Updates zero position offsets
     - Sets the axis to closed loop by calling Axis::setClosedLoop()
     - Calls Axis::fetchState() and logs the state of the axis
     - Sends axis config to ODrives on each axis
     - Checks if ODrive response has a nonzero length
     - If the response length is nonzero, sending the axis config failed
     - ODrive response is logged if setting the axis config failed

.. code-block:: none

   • Axis::fetchOffset()
     - Gets the position estimate of the ODrive, converts it to a float, and stores it in offset

.. code-block:: none

   • Axis::reset()
     - Attempts to reset the ODrive by setting controller, encoder, motor, and general error flags to 0
     - Sends “sc:\n” to the ODrive, clearing communications

.. code-block:: none

   • Axis::setClosedLoop()
     - Sends data to the ODrive, requesting closed loop state

.. code-block:: none

   • Axis::fetchState()
     - Returns an integer state of the ODrive’s control mode

.. code-block:: none

   • Axis::move(float pos)
     - Checks if provided position is different from target position
     - If positions are different, sets target position to pos, then sends targetPos + offset to the ODrive

.. code-block:: none

   • Axis::getOffset()
     - Returns the current offset from starting position

.. code-block:: none

   • Axis::fetchError()
     - Returns ODrive error as an integer

.. code-block:: none

   • Axis::setSpeed(float speed)
     - Takes a float in, multiplies it by static GLOBAL_SPEED variable, then logs the calculated speed
     - Sets speed of each axis of the ODrive to calculated speed


kinematics.cpp
~~~~~~~~~~~~~~

.. code-block:: none

   • Kinematics::translate(int leg, float xIn, float yIn, float zIn, float roll, float pitch, float yawIn)
     - Inverse kinematics calculation that takes in the requested position of a leg, then outputs necessary
       hip/shoulder/knee angles
     - Figure it out if you really need to. You probably don’t.

.. code-block:: none

   • Kinematics::walk(int RFB, int RLR, int LT, float IMUpitch, float IMUroll)
     - (Implementation handles walking logic)

.. code-block:: none

   • Kinematics::pushUp(bool cross_press, bool triangle_press, float IMUpitch, float IMUroll)
     - Checks if cross or triangle are pressed. If cross is pressed, all legs go down. If triangle is pressed, only the
       back legs go down. If both are pressed, all legs go down.
     - Corrects for roll and pitch based on IMU data, scaled by 0.3

.. code-block:: none

   • Kinematics::dance(bool up, bool down, bool left, bool right, float IMUpitch, float IMUroll)
     - Loops through step 1–4
     - Based on the given D-pad input, chooses one of the following


leg.cpp
~~~~~~~

.. code-block:: none

   • Leg::move(JointAngles angles)
     - Moves the leg by updating all three joint motors (hip, shoulder, knee) to their respective target angles.
     - Calls hip.move(), shoulder.move(), and knee.move() with the given joint angles.


log.cpp
~~~~~~~

.. code-block:: none

   • Log(const char* format, ...)
     - Variadic function that prints formatted messages to the serial monitor.
     - Executes only when the DEBUG flag is enabled to prevent slowdowns during normal operation.
     - Uses va_list to handle variable arguments and SerialMon.vprintf() for formatted output.


main.cpp
~~~~~~~~

.. code-block:: none

   • setup()
     - Initializes the serial monitor at 115200 baud.
     - Prints crash reports if available.
     - Logs initialization message.
     - Calls sparky.setup() to initialize all components.

.. code-block:: none

   • loop()
     - Main loop repeatedly calls sparky.update() to process robot logic and motion control.


odrive.cpp
~~~~~~~~~~

.. code-block:: none

   • ODrive::init()
     - Sends configuration commands to the ODrive hardware from a predefined array.
     - Logs any failed configuration responses.
     - Initializes both axis0 and axis1.
     - Sends "ss" to save the ODrive configuration.
     - Marks the ODrive as initialized.

.. code-block:: none

   • ODrive::connect()
     - Establishes serial communication with the ODrive.
     - Flushes input/output buffers to clear any previous communication.
     - Reads firmware version and serial number to verify a valid connection.
     - If connection succeeds, reconstructs and stores the serial number.
     - Logs connection status and initializes the ODrive.
     - Sets timeouts and flags _connected to true.


sparky.cpp
~~~~~~~~~~

.. code-block:: none

   • Sparky::Sparky()
     - Constructor initializing six ODrive objects, four Leg objects, and a Kinematics instance.
     - Maps legs and axes to specific ODrives and serial interfaces.

.. code-block:: none

   • Sparky::setup()
     - Initializes serial communication for USB and six ODrives.
     - Initializes and configures the MPU6050 IMU with acceleration and gyro ranges.
     - Connects to each ODrive by calling od.connect().
     - Logs setup status.

.. code-block:: none

   • Sparky::update()
     - Called every TICK_MS (10ms).
     - Updates IMU readings, calculates pitch and roll using a complementary filter.
     - Handles remote control communication and updates robot motion mode.
     - Commands kinematics functions (walk, push-up, dance) based on mode.
     - Manages ODrive and axis error checking, reconnections, and resets as needed.
     - Handles MotionProtocol messages received via SerialUSB.
     - Sends ODrive connection and error status back through USB using FlatBuffers.

.. code-block:: none

   • Sparky::setSpeed(float speed)
     - Updates the motion speed scaling factor across all ODrives.
     - Calls axis0.setSpeed() and axis1.setSpeed() for every ODrive.


utils.cpp
~~~~~~~~~

.. code-block:: none

   • thresholdStick(int pos)
     - Applies a deadzone threshold (±10) to controller stick input.
     - Returns 0 if the stick value is within threshold; otherwise returns the raw value.

.. code-block:: none

   • filter(float prevValue, float currentValue, int filter)
     - Applies a simple weighted moving average filter.
     - Smooths motion input by blending previous and current values based on filter weight.

Platform Repository
===================
platform/MotionProtocol
=======================

High-Level Code Overview
------------------------

Message.py
~~~~~~~~~~
Automatically generated FlatBuffers module defining the `Message` object
for robot communication. Handles serialization and deserialization of
messages containing type information and optional `Remote` data.

Message.pyi
~~~~~~~~~~~
Type stub providing signatures and type hints for the generated
`Message` FlatBuffers class and helper functions.

MessageType.py
~~~~~~~~~~~~~~
Enumerates message types for FlatBuffers communication schema. Defines
integer constants representing message categories.

MessageType.pyi
~~~~~~~~~~~~~~~
Type stub defining type hints and integer constants for message types.

ODriveStatus.py
~~~~~~~~~~~~~~~
Automatically generated FlatBuffers module defining the structure for
ODrive connection and error reporting across six motor controllers.
Contains helper functions for FlatBuffers serialization.

ODriveStatus.pyi
~~~~~~~~~~~~~~~~
Type stub defining methods and type hints for the `ODriveStatus` class
and its FlatBuffers builder helper functions.

Remote.py
~~~~~~~~~~

Automatically generated FlatBuffers module defining the `Remote` class
used for representing remote control input state (buttons, sticks, and
triggers). Includes methods for reading, constructing, and serializing
controller data fields.

Remote.pyi
~~~~~~~~~~

Type stub defining method signatures, field types, and FlatBuffers
builder helpers for the Remote object.


Low-Level Code Overview
------------------------

Message.py
~~~~~~~~~~

.. code-block:: none

   • Message.GetRootAs(buf, offset=0)
     - Returns a new Message instance from a FlatBuffer, initializing its internal table at the given offset.

.. code-block:: none

   • Message.GetRootAsMessage(buf, offset=0)
     - Deprecated version of GetRootAs maintained for backward compatibility.

.. code-block:: none

   • Message.Init(buf, pos)
     - Initializes the FlatBuffers table reference for the Message.

.. code-block:: none

   • Message.Type()
     - Returns the message type as an integer enum (e.g., UNKNOWN, REMOTE).

.. code-block:: none

   • Message.Remote()
     - Returns a Remote object if present; otherwise returns None.

.. code-block:: none

   • MessageStart(builder)
     - Begins FlatBuffers object construction for a Message.

.. code-block:: none

   • MessageAddType(builder, type)
     - Adds the message type field to the FlatBuffer.

.. code-block:: none

   • MessageAddRemote(builder, remote)
     - Adds a Remote object reference to the FlatBuffer.

.. code-block:: none

   • MessageEnd(builder)
     - Finishes and returns the offset of the Message object.


Message.pyi
~~~~~~~~~~~

.. code-block:: none

   • Message.GetRootAs(buf, offset)
     - Type-annotated version of GetRootAs that returns a Message instance.

.. code-block:: none

   • Message.Init(buf, pos)
     - Type-hinted initialization of internal buffer for the Message.

.. code-block:: none

   • Message.Type()
     - Returns a literal type from MessageType (UNKNOWN or REMOTE).

.. code-block:: none

   • Message.Remote()
     - Returns a Remote object or None, with type hints applied.

.. code-block:: none

   • MessageStart(builder)
     - Type-annotated helper to start FlatBuffer construction.

.. code-block:: none

   • MessageAddType(builder, type)
     - Adds the type field with explicit MessageType hint.

.. code-block:: none

   • MessageAddRemote(builder, remote)
     - Adds a Remote reference using FlatBuffers offset alias.


MessageType.py
~~~~~~~~~~~~~~

.. code-block:: none

   • MessageType.UNKNOWN
     - Integer constant (0) representing an unknown message type.

.. code-block:: none

   • MessageType.REMOTE
     - Integer constant (1) representing a remote control message.


MessageType.pyi
~~~~~~~~~~~~~~~

.. code-block:: none

   • MessageType.UNKNOWN
     - Type stub constant matching generated FlatBuffers enum.

.. code-block:: none

   • MessageType.REMOTE
     - Type stub constant representing a remote message type.


ODriveStatus.py
~~~~~~~~~~~~~~~

.. code-block:: none

   • ODriveStatus.GetRootAs(buf, offset=0)
     - Initializes an ODriveStatus object from FlatBuffer data.

.. code-block:: none

   • ODriveStatus.GetRootAsODriveStatus(buf, offset=0)
     - Deprecated alias for GetRootAs for backward compatibility.

.. code-block:: none

   • ODriveStatus.Init(buf, pos)
     - Initializes the internal FlatBuffers table reference.

.. code-block:: none

   • ODriveStatus.Connected0() through ODriveStatus.Connected5()
     - Return boolean connection statuses for each of the six ODrives.

.. code-block:: none

   • ODriveStatus.Error00() through ODriveStatus.Error51()
     - Return integer error codes for each of the 12 ODrive axes.

.. code-block:: none

   • ODriveStatusStart(builder)
     - Begins FlatBuffers object creation for ODriveStatus.

.. code-block:: none

   • ODriveStatusAddConnectedX(builder, connectedX)
     - Adds boolean connection field X (0–5) to the FlatBuffer.

.. code-block:: none

   • ODriveStatusAddErrorXY(builder, errorXY)
     - Adds integer error code field for axis Y of ODrive X.

.. code-block:: none

   • ODriveStatusEnd(builder)
     - Finalizes the FlatBuffer construction and returns the offset.


ODriveStatus.pyi
~~~~~~~~~~~~~~~~

.. code-block:: none

   • ODriveStatus.GetRootAs(buf, offset)
     - Type-stub version returning ODriveStatus instance.

.. code-block:: none

   • ODriveStatus.Init(buf, pos)
     - Initializes typed FlatBuffers table.

.. code-block:: none

   • ODriveStatus.Connected0()–Connected5()
     - Return typed bool values for ODrive connection flags.

.. code-block:: none

   • ODriveStatus.Error00()–Error51()
     - Return typed int values for error codes.

.. code-block:: none

   • ODriveStatusStart(builder)
     - Type-stub FlatBuffers builder initialization.

.. code-block:: none

   • ODriveStatusAddConnectedX(builder, connectedX)
     - Adds connection field with type annotations.

.. code-block:: none

   • ODriveStatusAddErrorXY(builder, errorXY)
     - Adds error fields with proper FlatBuffers slot typing.

.. code-block:: none

   • ODriveStatusEnd(builder)
     - Finalizes FlatBuffers construction in the type stub.

Remote.py
~~~~~~~~~~

.. code-block:: none

   • Remote.GetRootAs(buf, offset=0)
     - Initializes a Remote object from FlatBuffer data at the given offset.

.. code-block:: none

   • Remote.GetRootAsRemote(buf, offset=0)
     - Deprecated alias for GetRootAs, kept for backward compatibility.

.. code-block:: none

   • Remote.Init(buf, pos)
     - Initializes FlatBuffers table reference for Remote.

.. code-block:: none

   • Remote.Enabled()
     - Returns True if the remote control is enabled.

.. code-block:: none

   • Remote.Mode()
     - Returns the current operating mode as an integer flag.

.. code-block:: none

   • Remote.Rlr(), Remote.Rfb(), Remote.Rt()
     - Return signed or unsigned 8-bit integer stick or trigger values for the right side controls.

.. code-block:: none

   • Remote.Llr(), Remote.Lfb(), Remote.Lt()
     - Return signed or unsigned 8-bit integer stick or trigger values for the left side controls.

.. code-block:: none

   • Remote.DpadU(), Remote.DpadD(), Remote.DpadL(), Remote.DpadR()
     - Return boolean values for each directional pad input.

.. code-block:: none

   • Remote.Triangle(), Remote.Cross(), Remote.Square(), Remote.Circle()
     - Return boolean values representing button presses.

.. code-block:: none

   • RemoteStart(builder)
     - Begins FlatBuffers object creation for Remote.

.. code-block:: none

   • RemoteAddEnabled(builder, enabled)
     - Adds a boolean indicating whether the remote is active.

.. code-block:: none

   • RemoteAddMode(builder, mode)
     - Adds mode selection field to the FlatBuffer.

.. code-block:: none

   • RemoteAddRlr(), RemoteAddRfb(), RemoteAddRt()
     - Add right stick and trigger fields to the FlatBuffer.

.. code-block:: none

   • RemoteAddLlr(), RemoteAddLfb(), RemoteAddLt()
     - Add left stick and trigger fields to the FlatBuffer.

.. code-block:: none

   • RemoteAddDpadU(), RemoteAddDpadD(), RemoteAddDpadL(), RemoteAddDpadR()
     - Add boolean D-pad state fields to the FlatBuffer.

.. code-block:: none

   • RemoteAddTriangle(), RemoteAddCross(), RemoteAddSquare(), RemoteAddCircle()
     - Add boolean button state fields to the FlatBuffer.

.. code-block:: none

   • RemoteEnd(builder)
     - Finalizes and returns the offset of the Remote object in the FlatBuffer.


Remote.pyi
~~~~~~~~~~

.. code-block:: none

   • Remote.GetRootAs(buf, offset)
     - Returns a Remote object from a typed buffer.

.. code-block:: none

   • Remote.Init(buf, pos)
     - Initializes a typed FlatBuffers table reference.

.. code-block:: none

   • Remote.Enabled()
     - Returns a typed boolean for the enabled state.

.. code-block:: none

   • Remote.Mode(), Rlr(), Rfb(), Rt(), Llr(), Lfb(), Lt()
     - Typed integer accessors for analog stick and trigger values.

.. code-block:: none

   • Remote.DpadU(), DpadD(), DpadL(), DpadR()
     - Typed boolean accessors for directional pad inputs.

.. code-block:: none

   • Remote.Triangle(), Cross(), Square(), Circle()
     - Typed boolean accessors for button inputs.

.. code-block:: none

   • RemoteStart(builder)
     - Type-stub FlatBuffers builder start helper.

.. code-block:: none

   • RemoteAddEnabled(), RemoteAddMode(), RemoteAddRlr(), RemoteAddRfb(), RemoteAddRt()
     - Type-stub helpers for adding right-hand control fields.

.. code-block:: none

   • RemoteAddLlr(), RemoteAddLfb(), RemoteAddLt()
     - Type-stub helpers for adding left-hand control fields.

.. code-block:: none

   • RemoteAddDpadU(), RemoteAddDpadD(), RemoteAddDpadL(), RemoteAddDpadR()
     - Type-stub helpers for D-pad field creation.

.. code-block:: none

   • RemoteAddTriangle(), RemoteAddCross(), RemoteAddSquare(), RemoteAddCircle()
     - Type-stub helpers for button field creation.

.. code-block:: none

   • RemoteEnd(builder)
     - Finalizes the typed FlatBuffers Remote object.


platform/robot
==============

High-Level Code Overview
------------------------

audio_manager.py
~~~~~~~~~~~~~~~~
Handles audio playback for the robot.  
Initializes Pygame mixer, loads sound effects and songs, manages volumes, and provides
methods for playing mode-specific or random sounds.

controller.py
~~~~~~~~~~~~~
Manages DualShock/PS4 controller input via `evdev`.  
Reads button and stick states asynchronously, updates LEDs, and communicates input
state to the robot. Includes asynchronous polling for controller battery status.

mode.py
~~~~~~~
Defines the base `Mode` class, representing a robot operating mode (e.g., walk, push-up).  
Provides coroutine management, loop setup, and graceful start/stop task handling.

motion.py
~~~~~~~~~
Handles serial communication between the robot and the Teensy motion controller.  
Sends FlatBuffers messages containing remote input data and receives ODrive status responses.  
Implements connection, reconnection, and command streaming via `aioserial`.

sparky.py
~~~~~~~~~
Main robot orchestration file.  
Initializes subsystems (UI, controller, motion, and mode).  
Manages runtime tasks, LED heartbeat indicator, and dynamic mode loading.

ui.py
~~~~~

Provides the PyQt6-based graphical user interface for the robot.  
Defines the `MainWindow` class that manages enable/disable buttons, mode selection, and
asynchronous communication with the `Sparky` backend.  
Integrates `qasync` to bridge the PyQt6 event loop with asyncio for concurrent UI and
robot control operation.

Low-Level Code Overview
------------------------

audio_manager.py
~~~~~~~~~~~~~~~~

.. code-block:: none

   • AudioManager.__init__(self)
     - Initializes the Pygame mixer and loads all sound effects and songs.
     - Sets individual volume levels and maps sound keys to audio objects.

.. code-block:: none

   • AudioManager.play_sound(self, sound_key)
     - Plays the sound corresponding to the provided key if audio is enabled.

.. code-block:: none

   • AudioManager.play_mode_sounds(self, mode)
     - Maps numeric mode IDs (0–5) to mode switch sounds and plays the associated clip.

.. code-block:: none

   • AudioManager.play_songs(self, song)
     - Plays a random or specific song depending on the input argument.


controller.py
~~~~~~~~~~~~~

.. code-block:: none

   • ControllerLED.__init__(self, base_path)
     - Initializes LED color paths (red, green, blue) using the system device path.

.. code-block:: none

   • ControllerLED._write_color(self, path, value)
     - Writes a brightness value asynchronously to the LED color file.

.. code-block:: none

   • ControllerLED.set_color(self, color)
     - Sets LED color by writing RGB values asynchronously.

.. code-block:: none

   • Controller.__init__(self, robot)
     - Initializes controller input device and LED.
     - Reads initial button and axis states.
     - Creates asyncio event flag `is_ready`.

.. code-block:: none

   • Controller._find_controller_dev(self)
     - Scans available input devices and returns the correct PS4 controller device.

.. code-block:: none

   • Controller.update_event(self, event)
     - Updates controller state fields (buttons, sticks, triggers) from evdev input events.

.. code-block:: none

   • Controller.events(self)
     - Asynchronously reads controller events in real time and updates robot input.

.. code-block:: none

   • Controller.polling(self)
     - Asynchronously polls controller battery level once per minute.

.. code-block:: none

   • Controller.stop(self)
     - Placeholder for stopping controller event processing.

.. code-block:: none

   • Controller.__str__(self)
     - Returns a formatted string summarizing controller input states.


mode.py
~~~~~~~

.. code-block:: none

   • Mode.__init__(self, robot)
     - Initializes a Mode with references to the robot and motion subsystem.

.. code-block:: none

   • Mode.start(self)
     - Coroutine placeholder to be implemented by derived mode classes.

.. code-block:: none

   • Mode._run(self)
     - Executes the mode’s coroutine and handles cancellation.

.. code-block:: none

   • Mode.run(self)
     - Creates a new asyncio loop and runs the mode coroutine indefinitely.

.. code-block:: none

   • Mode.stop(self)
     - Cancels all running tasks within the mode’s event loop and stops execution.


motion.py
~~~~~~~~~

.. code-block:: none

   • Motion.__init__(self, robot)
     - Initializes serial communication and sets up remote control state variables.

.. code-block:: none

   • Motion._connect(self)
     - Establishes a serial connection to the Teensy motion controller.

.. code-block:: none

   • Motion.reconnect(self)
     - Attempts to reconnect indefinitely if serial communication fails.

.. code-block:: none

   • Motion._find_serial_dev(self)
     - Scans available ports for a Teensyduino device and returns its path.

.. code-block:: none

   • Motion.move(self, rfb, rlr, lfb, llr, rt, lt, dpad_u, dpad_d, dpad_l, dpad_r, triangle, cross, square, circle)
     - Updates current controller state values for transmission to the motion controller.

.. code-block:: none

   • Motion.stop(self)
     - Resets all control input values to their neutral state.

.. code-block:: none

   • Motion.run(self)
     - Builds FlatBuffers messages with Remote data.
     - Sends serialized data to the Teensy controller and reads ODrive status responses.
     - Handles reconnection and serial errors gracefully.
     - Runs continuously at 10 Hz (approx.) until cancelled.


sparky.py
~~~~~~~~~

.. code-block:: none

   • Sparky.__init__(self)
     - Initializes thread pool executor and sets default mode and enable state.

.. code-block:: none

   • Sparky.__aenter__(self)
     - Asynchronous context entry method for use with `async with`.

.. code-block:: none

   • Sparky.__aexit__(self, exc_type, exc, tb)
     - Handles cleanup and exception printing on context exit.

.. code-block:: none

   • Sparky.set_enabled(self, en)
     - Dynamically loads and starts a robot mode if `en` is True.
     - Stops motion and active mode if disabled.

.. code-block:: none

   • Sparky.heartbeat(self)
     - Controls LED heartbeat pattern to indicate robot enable/disable status.

.. code-block:: none

   • Sparky.move(self, *args, **kwargs)
     - Passes movement commands to the motion subsystem.

.. code-block:: none

   • Sparky.run(self)
     - Starts UI and main asyncio event loop.
     - Initializes controller, motion, and heartbeat tasks.
     - Runs the UI event loop indefinitely.

.. code-block:: none

   • Sparky.stop(self)
     - Cancels all active tasks, resets controller LED, and shuts down executor threads.

ui.py
~~~~~

.. code-block:: none

   • MainWindow.__init__(self, robot)
     - Loads the UI layout from `platform.ui`.
     - Initializes buttons for enabling/disabling the robot and selecting operating modes.
     - Connects button click events to asynchronous handlers.

.. code-block:: none

   • MainWindow.set_mode_buttons_disabled(self, disabled)
     - Enables or disables all mode selection buttons based on the `disabled` flag.

.. code-block:: none

   • MainWindow.set_enabled(self, en)
     - Asynchronously enables or disables the robot by calling `robot.set_enabled(en)`.
     - Updates the enable/disable button states and mode button interactivity.

.. code-block:: none

   • MainWindow.on_enable(self)
     - Async slot triggered when the "Enable" button is clicked.
     - Determines the selected mode and starts the robot in that mode.

.. code-block:: none

   • MainWindow.on_disable(self)
     - Async slot triggered when the "Disable" button is clicked.
     - Calls `set_enabled(False)` to disable the robot safely.

.. code-block:: none

   • MainWindow.on_mode_button(self)
     - Handles mutual exclusivity of mode selection buttons.
     - Ensures only one mode button can be active at a time.

.. code-block:: none

   • MainWindow.start(cls, robot)
     - Initializes the PyQt6 application and event loop via `QEventLoop`.
     - Creates and displays the main window instance connected to the given `Sparky` robot.

.. code-block:: none

   • MainWindow.closeEvent(self, event)
     - Handles window close events.
     - Stops the robot gracefully when the UI is closed.

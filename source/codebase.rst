Motion Repository
==================

High-Level Code Overview
-------------------------

Provides a general overview of each code file, what it does on a high level 

.. dropdown:: Axis.cpp
   Contains methods for initializing axis, fetching coordinates, and resetting ODrives. Contains method for sending position to ODrives.
   
   .. tab-set::

      .. tab-item:: Axis::Axis()

         .. code-block:: c

            Axis::Axis(Odrive& _odrive, int _id)

         Sets ODrive and id of axis to specified parameters (there are 2 ODrives per axis)

      .. tab-item:: Axis::init()

         .. code-block:: none

            - Checks for errors, then tries to reset them by calling ``Axis::reset()``

            - Updates zero position offsets

            - Sets the axis to closed loop by calling ``Axis::setClosedLoop()``

            - Calls ``Axis::fetchState()`` and logs the state of the axis

            - Sends axis config to ODrives on each axis

            - Checks if ODrive response has a nonzero length

            - If the response length is nonzero, sending the axis config failed

            - ODrive response is logged if setting the axis config failed

      .. tab-item:: Axis::fetchOffset()

         .. code-block:: none

            - Gets the position estimate of the ODrive, converts it to a float, and stores it in ``offset``

      .. tab-item:: Axis::reset()

         .. code-block:: none

            - Attempts to reset the ODrive by setting controller, encoder, motor, and general error flags to 0

            - Sends “sc:\n” to the ODrive, clearing communications

      .. tab-item:: Axis::setClosedLoop()

         .. code-block:: none

            - Sends data to the ODrive, requesting closed loop state

      .. tab-item:: Axis::fetchState()

         .. code-block:: none

            - Returns an integer state of the ODrive's control mode

      .. tab-item:: Axis::move(float pos)

         .. code-block:: none

            - Checks if provided position is different from target position

            - If positions are different, sets target position to ``pos``, then sends ``targetPos + offset`` to the ODrive

      .. tab-item:: Axis::getOffset()

         .. code-block:: none

            - Returns the current offset from starting position

      .. tab-item:: Axis::fetchError()

         .. code-block:: none

            - Returns ODrive error as an integer

      .. tab-item:: Axis::setSpeed(float speed)

         .. code-block:: none

            - Takes a float in, multiplies it by static ``GLOBAL_SPEED`` variable, then logs the calculated speed

            - Sets speed of each axis of the ODrive to calculated speed

.. dropdown:: kinematics.cpp
   Contains method for inverse kinematics calculations, which takes position (x, y, z, roll, pitch, yaw), and translates them to relative ODrive movements for each joint. Contains methods for walking, pushups, and dancing.

   .. tab-set::
   
      .. tab-item:: Kinematics::translate
      
         .. code-block:: c

            Kinematics::translate(int leg, float xIn, float yIn, float zIn, float roll, float pitch, float yawIn)

         - Inverse kinematics calculation that takes in the requested position of a leg, then outputs necessary hip/shoulder/knee angles

         - Figure it out if you really need to. You probably don't.

      .. tab-item:: Kinematics::walk
      
         .. code-block:: c

            Kinematics::walk(int RFB, int RLR, int LT, float IMUpitch, float IMUroll)

         (Implementation handles walking logic)

      .. tab-item:: Kinematics::pushUp
      
         .. code-block:: c

            Kinematics::pushUp(bool cross_press, bool triangle_press, float IMUpitch, float IMUroll)

         - Checks if cross or triangle are pressed. If cross is pressed, all legs go down. If triangle is pressed, only the back legs go down. If both are pressed, all legs go down.

         - Corrects for roll and pitch based on IMU data, scaled by 0.3

      .. tab-item:: Kinematics::dance
      
         .. code-block:: c

            Kinematics::dance(bool up, bool down, bool left, bool right, float IMUpitch, float IMUroll)

         - Loops through step 1-4

         - Based on the given D-pad input, chooses one of the following


.. dropdown:: leg.cpp

   Contains helper method for movement of all joints in a leg.

   .. tab-set::

      .. tab-item:: Leg::move(JointAngles angles)

         .. code-block:: c

            Leg::move(JointAngles angles)

         - Moves the leg by updating all three joint motors (hip, shoulder, knee) to their respective target angles.

         - Calls hip.move(), shoulder.move(), and knee.move() with the given joint angles.

.. dropdown:: log.cpp
   Contains helper method for logging errors/information while DEBUG flag is set. Running in debug mode slows serial communications for the rest of the robot.
   .. tab-set::

      .. tab-item:: Log(const char* format, ...)

         .. code-block:: none

            - Variadic function that prints formatted messages to the serial monitor.

            - Executes only when the DEBUG flag is enabled to prevent slowdowns during normal operation.

            - Uses ``va_list`` to handle variable arguments and ``SerialMon.vprintf()`` for formatted output.

.. dropdown:: main.cpp

   Initializes serial communications of the robot, then calls setup method in sparky.cpp. Contains loop code, which calls update method in sparky.cpp.

   .. tab-set::

      .. tab-item:: setup()

         .. code-block:: none

            - Initializes the serial monitor at 115200 baud.

            - Prints crash reports if available.

            - Logs initialization message.

            - Calls sparky.setup() to initialize all components.

      .. tab-item:: loop()

         .. code-block:: none

            - Main loop repeatedly calls sparky.update() to process robot logic and motion control.

.. dropdown:: odrive.cpp

   Contains methods for initializing and connecting to an ODrive. Initializes axis for each ODrive. Defines parameters for ODrive setup.

   .. tab-set::

      .. tab-item:: ODrive::init()

         .. code-block:: none

            - Sends configuration commands to the ODrive hardware from a predefined array.

            - Logs any failed configuration responses.

            - Initializes both ``axis0`` and ``axis1``.

            - Sends ``ss`` to save the ODrive configuration.

            - Marks the ODrive as initialized.

      .. tab-item:: ODrive::connect()

         .. code-block:: none

            - Establishes serial communication with the ODrive.

            - Flushes input/output buffers to clear any previous communication.

            - Reads firmware version and serial number to verify a valid connection.

            - If connection succeeds, reconstructs and stores the serial number.

            - Logs connection status and initializes the ODrive.

            - Sets timeouts and flags _connected to true.

.. dropdown:: sparky.cpp

   Contains setup method, which initializes all serial communications on the robot. Contains update method, which is called in the main.cpp loop. Update checks tick time so that the robot is updated every 10ms, then checks which mode (walk, pushup, dance) the robot is in. IMU data is sent to the respective kinematics function. The IMU is then updated with current values. Updates to internal states are then made (button presses, operation mode).

   .. tab-set::

      .. tab-item:: Sparky::Sparky()

         .. code-block:: none

            - Constructor initializing six ODrive objects, four Leg objects, and a Kinematics instance.

            - Maps legs and axes to specific ODrives and serial interfaces.

      .. tab-item:: Sparky::setup()

         .. code-block:: none

            - Initializes serial communication for USB and six ODrives.

            - Initializes and configures the MPU6050 IMU with acceleration and gyro ranges.

            - Connects to each ODrive by calling od.connect().

            - Logs setup status.

      .. tab-item:: Sparky::update()

         .. code-block:: none

            - Called every TICK_MS (10ms).

            - Updates IMU readings, calculates pitch and roll using a complementary filter.

            - Handles remote control communication and updates robot motion mode.

            - Commands kinematics functions (walk, push-up, dance) based on mode.

            - Manages ODrive and axis error checking, reconnections, and resets as needed.

            - Handles MotionProtocol messages received via SerialUSB.

            - Sends ODrive connection and error status back through USB using FlatBuffers.

      .. tab-item:: Sparky::setSpeed(float speed)

         .. code-block:: none

            - Updates the motion speed scaling factor across all ODrives.

            - Calls axis0.setSpeed() and axis1.setSpeed() for every ODrive.

.. dropdown:: utils.cpp

   Contains methods for trimming stick positions (controller deadzone) and filtering motions.

   .. tab-set::

      .. tab-item:: thresholdStick(int pos)

         .. code-block:: none

            - Applies a deadzone threshold (±10) to controller stick input.

            - Returns 0 if the stick value is within threshold; otherwise returns the raw value.

      .. tab-item:: filter()

         .. code-block:: c

            filter(float prevValue, float currentValue, int filter)

         - Applies a simple weighted moving average filter.

         - Smooths motion input by blending previous and current values based on filter weight.

High Level Summary
~~~~~~~~~~~~~~~~~~

Upon running setup in main.cpp, the following happen:

* A serial monitor is set up

* sparky.setup() is called

* In sparky.setup(), serial connections are created for all 6 of the ODrives. The IMU is then initialized, and all ODrive connections are made

The majority of functionality occurs in sparky.update(), which is called from main.loop(). The following happen:

* The current time is fetched. If it has been < 10ms since the last update, sparky does not update

* Ensures controller and ODrives are connected

* Checks current mode, then calls the respective method in kinematics.cpp (walk, pushUp, or dance)

* Checks the IMU for current roll/pitch, and stores them in class variables

* Checks for updates to controller state, and stores button values in class variables

* Checks for ODrive errors


Platform Repository
===================

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

.. dropdown:: Message.py

   Contains FlatBuffers methods for reading, constructing, and manipulating Message objects.

   .. tab-set::

      .. tab-item:: Message.GetRootAs(buf, offset=0)

         .. code-block:: none

            - Returns a new Message instance from a FlatBuffer, initializing its internal table at the given offset.

      .. tab-item:: Message.GetRootAsMessage(buf, offset=0)

         .. code-block:: none

            - Deprecated version of GetRootAs maintained for backward compatibility.

      .. tab-item:: Message.Init(buf, pos)

         .. code-block:: none

            - Initializes the FlatBuffers table reference for the Message.

      .. tab-item:: Message.Type()

         .. code-block:: none

            - Returns the message type as an integer enum (e.g., UNKNOWN, REMOTE).

      .. tab-item:: Message.Remote()

         .. code-block:: none

            - Returns a Remote object if present; otherwise returns None.

      .. tab-item:: MessageStart(builder)

         .. code-block:: none

            - Begins FlatBuffers object construction for a Message.

      .. tab-item:: MessageAddType(builder, type)

         .. code-block:: none

            - Adds the message type field to the FlatBuffer.

      .. tab-item:: MessageAddRemote(builder, remote)

         .. code-block:: none

            - Adds a Remote object reference to the FlatBuffer.

      .. tab-item:: MessageEnd(builder)

         .. code-block:: none

            - Finishes and returns the offset of the Message object.

.. dropdown:: Message.pyi

   Type stub providing signatures and type hints for the Message FlatBuffers class and helper functions.

   .. tab-set::

      .. tab-item:: Message.GetRootAs(buf, offset)

         .. code-block:: none

            - Type-annotated version of GetRootAs that returns a Message instance.

      .. tab-item:: Message.Init(buf, pos)

         .. code-block:: none

            - Type-hinted initialization of internal buffer for the Message.

      .. tab-item:: Message.Type()

         .. code-block:: none

            - Returns a literal type from MessageType (UNKNOWN or REMOTE).

      .. tab-item:: Message.Remote()

         .. code-block:: none

            - Returns a Remote object or None, with type hints applied.

      .. tab-item:: MessageStart(builder)

         .. code-block:: none

            - Type-annotated helper to start FlatBuffer construction.

      .. tab-item:: MessageAddType(builder, type)

         .. code-block:: none

            - Adds the type field with explicit MessageType hint.

      .. tab-item:: MessageAddRemote(builder, remote)

         .. code-block:: none

            - Adds a Remote reference using FlatBuffers offset alias.


.. dropdown:: MessageType.py

   Enumerates message types for FlatBuffers communication schema.

   .. tab-set::

      .. tab-item:: MessageType.UNKNOWN

         .. code-block:: none

            - Integer constant (0) representing an unknown message type.

      .. tab-item:: MessageType.REMOTE

         .. code-block:: none

            - Integer constant (1) representing a remote control message.

.. dropdown:: MessageType.pyi

   Type stub defining type hints and integer constants for message types.

   .. tab-set::

      .. tab-item:: MessageType.UNKNOWN

         .. code-block:: none

            - Type stub constant matching generated FlatBuffers enum.

      .. tab-item:: MessageType.REMOTE

         .. code-block:: none

            - Type stub constant representing a remote message type.


.. dropdown:: ODriveStatus.py

   Automatically generated FlatBuffers module defining the structure for ODrive connection and error reporting.

   .. tab-set::

      .. tab-item:: ODriveStatus.GetRootAs(buf, offset=0)

         .. code-block:: none

            - Initializes an ODriveStatus object from FlatBuffer data.

      .. tab-item:: ODriveStatus.GetRootAsODriveStatus(buf, offset=0)

         .. code-block:: none

            - Deprecated alias for GetRootAs for backward compatibility.

      .. tab-item:: ODriveStatus.Init(buf, pos)

         .. code-block:: none

            - Initializes the internal FlatBuffers table reference.

      .. tab-item:: ODriveStatus.Connected0() through Connected5()

         .. code-block:: none

            - Return boolean connection statuses for each of the six ODrives.

      .. tab-item:: ODriveStatus.Error00() through Error51()

         .. code-block:: none

            - Return integer error codes for each of the 12 ODrive axes.

      .. tab-item:: ODriveStatusStart(builder)

         .. code-block:: none

            - Begins FlatBuffers object creation for ODriveStatus.

      .. tab-item:: ODriveStatusAddConnectedX(builder, connectedX)

         .. code-block:: none

            - Adds boolean connection field X (0–5) to the FlatBuffer.

      .. tab-item:: ODriveStatusAddErrorXY(builder, errorXY)

         .. code-block:: none

            - Adds integer error code field for axis Y of ODrive X.

      .. tab-item:: ODriveStatusEnd(builder)

         .. code-block:: none

            - Finalizes the FlatBuffer construction and returns the offset.

.. dropdown:: ODriveStatus.pyi

   Type stub defining methods and type hints for the ODriveStatus class.

   .. tab-set::

      .. tab-item:: ODriveStatus.GetRootAs(buf, offset)

         .. code-block:: none

            - Type-stub version returning ODriveStatus instance.

      .. tab-item:: ODriveStatus.Init(buf, pos)

         .. code-block:: none

            - Initializes typed FlatBuffers table.

      .. tab-item:: ODriveStatus.Connected0()–Connected5()

         .. code-block:: none

            - Return typed bool values for ODrive connection flags.

      .. tab-item:: ODriveStatus.Error00()–Error51()

         .. code-block:: none

            - Return typed int values for error codes.

      .. tab-item:: ODriveStatusStart(builder)

         .. code-block:: none

            - Type-stub FlatBuffers builder initialization.

      .. tab-item:: ODriveStatusAddConnectedX(builder, connectedX)

         .. code-block:: none

            - Adds connection field with type annotations.

      .. tab-item:: ODriveStatusAddErrorXY(builder, errorXY)

         .. code-block:: none

            - Adds error fields with proper FlatBuffers slot typing.

      .. tab-item:: ODriveStatusEnd(builder)

         .. code-block:: none

            - Finalizes FlatBuffers construction in the type stub.

.. dropdown:: Remote.py

   Automatically generated FlatBuffers module defining the Remote class for remote control input state.

   .. tab-set::

      .. tab-item:: Remote.GetRootAs(buf, offset=0)

         .. code-block:: none

            - Initializes a Remote object from FlatBuffer data at the given offset.

      .. tab-item:: Remote.GetRootAsRemote(buf, offset=0)

         .. code-block:: none

            - Deprecated alias for GetRootAs, kept for backward compatibility.

      .. tab-item:: Remote.Init(buf, pos)

         .. code-block:: none

            - Initializes FlatBuffers table reference for Remote.

      .. tab-item:: Remote.Enabled()

         .. code-block:: none

            - Returns True if the remote control is enabled.

      .. tab-item:: Remote.Mode()

         .. code-block:: none

            - Returns the current operating mode as an integer flag.

      .. tab-item:: Remote.Rlr(), Rfb(), Rt()

         .. code-block:: none

            - Return signed or unsigned 8-bit integer stick or trigger values for the right side controls.

      .. tab-item:: Remote.Llr(), Lfb(), Lt()

         .. code-block:: none

            - Return signed or unsigned 8-bit integer stick or trigger values for the left side controls.

      .. tab-item:: Remote.DpadU(), DpadD(), DpadL(), DpadR()

         .. code-block:: none

            - Return boolean values for each directional pad input.

      .. tab-item:: Remote.Triangle(), Cross(), Square(), Circle()

         .. code-block:: none

            - Return boolean values representing button presses.

      .. tab-item:: RemoteStart(builder)

         .. code-block:: none

            - Begins FlatBuffers object creation for Remote.

      .. tab-item:: RemoteAddEnabled(builder, enabled)

         .. code-block:: none

            - Adds a boolean indicating whether the remote is active.

      .. tab-item:: RemoteAddMode(builder, mode)

         .. code-block:: none

            - Adds mode selection field to the FlatBuffer.

      .. tab-item:: RemoteAddRlr(), RemoteAddRfb(), RemoteAddRt()

         .. code-block:: none

            - Add right stick and trigger fields to the FlatBuffer.

      .. tab-item:: RemoteAddLlr(), RemoteAddLfb(), RemoteAddLt()

         .. code-block:: none

            - Add left stick and trigger fields to the FlatBuffer.

      .. tab-item:: RemoteAddDpadU(), RemoteAddDpadD(), RemoteAddDpadL(), RemoteAddDpadR()

         .. code-block:: none

            - Add boolean D-pad state fields to the FlatBuffer.

      .. tab-item:: RemoteAddTriangle(), RemoteAddCross(), RemoteAddSquare(), RemoteAddCircle()

         .. code-block:: none

            - Add boolean button state fields to the FlatBuffer.

      .. tab-item:: RemoteEnd(builder)

         .. code-block:: none

            - Finalizes and returns the offset of the Remote object in the FlatBuffer.

.. dropdown:: Remote.pyi

   Type stub defining method signatures and type hints for the Remote object.

   .. tab-set::

      .. tab-item:: Remote.GetRootAs(buf, offset)

         .. code-block:: none

            - Returns a Remote object from a typed buffer.

      .. tab-item:: Remote.Init(buf, pos)

         .. code-block:: none

            - Initializes a typed FlatBuffers table reference.

      .. tab-item:: Remote.Enabled()

         .. code-block:: none

            - Returns a typed boolean for the enabled state.

      .. tab-item:: Remote.Mode(), Rlr(), Rfb(), Rt(), Llr(), Lfb(), Lt()

         .. code-block:: none

            - Typed integer accessors for analog stick and trigger values.

      .. tab-item:: Remote.DpadU(), DpadD(), DpadL(), DpadR()

         .. code-block:: none

            - Typed boolean accessors for directional pad inputs.

      .. tab-item:: Remote.Triangle(), Cross(), Square(), Circle()

         .. code-block:: none

            - Typed boolean accessors for button inputs.

      .. tab-item:: RemoteStart(builder)

         .. code-block:: none

            - Type-stub FlatBuffers builder start helper.

      .. tab-item:: RemoteAddEnabled(), RemoteAddMode(), RemoteAddRlr(), RemoteAddRfb(), RemoteAddRt()

         .. code-block:: none

            - Type-stub helpers for adding right-hand control fields.

      .. tab-item:: RemoteAddLlr(), RemoteAddLfb(), RemoteAddLt()

         .. code-block:: none

            - Type-stub helpers for adding left-hand control fields.

      .. tab-item:: RemoteAddDpadU(), RemoteAddDpadD(), RemoteAddDpadL(), RemoteAddDpadR()

         .. code-block:: none

            - Type-stub helpers for D-pad field creation.

      .. tab-item:: RemoteAddTriangle(), RemoteAddCross(), RemoteAddSquare(), RemoteAddCircle()

         .. code-block:: none

            - Type-stub helpers for button field creation.

      .. tab-item:: RemoteEnd(builder)

         .. code-block:: none

            - Finalizes the typed FlatBuffers Remote object.


Robot
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

.. dropdown:: audio_manager.py

   Handles audio playback for the robot, managing sound effects and songs.

   .. tab-set::

      .. tab-item:: AudioManager.__init__(self)

         .. code-block:: none

            - Initializes the Pygame mixer and loads all sound effects and songs.
            - Sets individual volume levels and maps sound keys to audio objects.

      .. tab-item:: AudioManager.play_sound(self, sound_key)

         .. code-block:: none

            - Plays the sound corresponding to the provided key if audio is enabled.

      .. tab-item:: AudioManager.play_mode_sounds(self, mode)

         .. code-block:: none

            - Maps numeric mode IDs (0–5) to mode switch sounds and plays the associated clip.

      .. tab-item:: AudioManager.play_songs(self, song)

         .. code-block:: none

            - Plays a random or specific song depending on the input argument.

.. dropdown:: controller.py

   Manages DualShock/PS4 controller input and LED control.

   .. tab-set::

      .. tab-item:: ControllerLED.__init__(self, base_path)

         .. code-block:: none

            - Initializes LED color paths (red, green, blue) using the system device path.

      .. tab-item:: ControllerLED._write_color(self, path, value)

         .. code-block:: none

            - Writes a brightness value asynchronously to the LED color file.

      .. tab-item:: ControllerLED.set_color(self, color)

         .. code-block:: none

            - Sets LED color by writing RGB values asynchronously.

      .. tab-item:: Controller.__init__(self, robot)

         .. code-block:: none

            - Initializes controller input device and LED.
            - Reads initial button and axis states.
            - Creates asyncio event flag ``is_ready``.

      .. tab-item:: Controller._find_controller_dev(self)

         .. code-block:: none

            - Scans available input devices and returns the correct PS4 controller device.

      .. tab-item:: Controller.update_event(self, event)

         .. code-block:: none

            - Updates controller state fields (buttons, sticks, triggers) from evdev input events.

      .. tab-item:: Controller.events(self)

         .. code-block:: none

            - Asynchronously reads controller events in real time and updates robot input.

      .. tab-item:: Controller.polling(self)

         .. code-block:: none

            - Asynchronously polls controller battery level once per minute.

      .. tab-item:: Controller.stop(self)

         .. code-block:: none

            - Placeholder for stopping controller event processing.

      .. tab-item:: Controller.__str__(self)

         .. code-block:: none

            - Returns a formatted string summarizing controller input states.


.. dropdown:: mode.py

   Defines the base Mode class for robot operating modes.

   .. tab-set::

      .. tab-item:: Mode.__init__(self, robot)

         .. code-block:: none

            - Initializes a Mode with references to the robot and motion subsystem.

      .. tab-item:: Mode.start(self)

         .. code-block:: none

            - Coroutine placeholder to be implemented by derived mode classes.

      .. tab-item:: Mode._run(self)

         .. code-block:: none

            - Executes the mode's coroutine and handles cancellation.

      .. tab-item:: Mode.run(self)

         .. code-block:: none

            - Creates a new asyncio loop and runs the mode coroutine indefinitely.

      .. tab-item:: Mode.stop(self)

         .. code-block:: none

            - Cancels all running tasks within the mode's event loop and stops execution.


.. dropdown:: motion.py

   Handles serial communication between the robot and the Teensy motion controller.

   .. tab-set::

      .. tab-item:: Motion.__init__(self, robot)

         .. code-block:: none

            - Initializes serial communication and sets up remote control state variables.

      .. tab-item:: Motion._connect(self)

         .. code-block:: none

            - Establishes a serial connection to the Teensy motion controller.

      .. tab-item:: Motion.reconnect(self)

         .. code-block:: none

            - Attempts to reconnect indefinitely if serial communication fails.

      .. tab-item:: Motion._find_serial_dev(self)

         .. code-block:: none

            - Scans available ports for a Teensyduino device and returns its path.

      .. tab-item:: Motion.move(self, rfb, rlr, lfb, llr, rt, lt, dpad_u, dpad_d, dpad_l, dpad_r, triangle, cross, square, circle)

         .. code-block:: none

            - Updates current controller state values for transmission to the motion controller.

      .. tab-item:: Motion.stop(self)

         .. code-block:: none

            - Resets all control input values to their neutral state.

      .. tab-item:: Motion.run(self)

         .. code-block:: none

            - Builds FlatBuffers messages with Remote data.
            - Sends serialized data to the Teensy controller and reads ODrive status responses.
            - Handles reconnection and serial errors gracefully.
            - Runs continuously at 10 Hz (approx.) until cancelled.


.. dropdown:: sparky.py

   Main robot orchestration file for system initialization and control.

   .. tab-set::

      .. tab-item:: Sparky.__init__(self)

         .. code-block:: none

            - Initializes thread pool executor and sets default mode and enable state.

      .. tab-item:: Sparky.__aenter__(self)

         .. code-block:: none

            - Asynchronous context entry method for use with ``async with``.

      .. tab-item:: Sparky.__aexit__(self, exc_type, exc, tb)

         .. code-block:: none

            - Handles cleanup and exception printing on context exit.

      .. tab-item:: Sparky.set_enabled(self, en)

         .. code-block:: none

            - Dynamically loads and starts a robot mode if ``en`` is True.
            - Stops motion and active mode if disabled.

      .. tab-item:: Sparky.heartbeat(self)

         .. code-block:: none

            - Controls LED heartbeat pattern to indicate robot enable/disable status.

      .. tab-item:: Sparky.move(self, *args, **kwargs)

         .. code-block:: none

            - Passes movement commands to the motion subsystem.

      .. tab-item:: Sparky.run(self)

         .. code-block:: none

            - Starts UI and main asyncio event loop.
            - Initializes controller, motion, and heartbeat tasks.
            - Runs the UI event loop indefinitely.

      .. tab-item:: Sparky.stop(self)

         .. code-block:: none

            - Cancels all active tasks, resets controller LED, and shuts down executor threads.

.. dropdown:: ui.py

   Provides the PyQt6-based graphical user interface for the robot.

   .. tab-set::

      .. tab-item:: MainWindow.__init__(self, robot)

         .. code-block:: none

            - Loads the UI layout from ``platform.ui``.
            - Initializes buttons for enabling/disabling the robot and selecting operating modes.
            - Connects button click events to asynchronous handlers.

      .. tab-item:: MainWindow.set_mode_buttons_disabled(self, disabled)

         .. code-block:: none

            - Enables or disables all mode selection buttons based on the ``disabled`` flag.

      .. tab-item:: MainWindow.set_enabled(self, en)

         .. code-block:: none

            - Asynchronously enables or disables the robot by calling ``robot.set_enabled(en)``.
            - Updates the enable/disable button states and mode button interactivity.

      .. tab-item:: MainWindow.on_enable(self)

         .. code-block:: none

            - Async slot triggered when the "Enable" button is clicked.
            - Determines the selected mode and starts the robot in that mode.

      .. tab-item:: MainWindow.on_disable(self)

         .. code-block:: none

            - Async slot triggered when the "Disable" button is clicked.
            - Calls ``set_enabled(False)`` to disable the robot safely.

      .. tab-item:: MainWindow.on_mode_button(self)

         .. code-block:: none

            - Handles mutual exclusivity of mode selection buttons.
            - Ensures only one mode button can be active at a time.

      .. tab-item:: MainWindow.start(cls, robot)

         .. code-block:: none

            - Initializes the PyQt6 application and event loop via ``QEventLoop``.
            - Creates and displays the main window instance connected to the given ``Sparky`` robot.

      .. tab-item:: MainWindow.closeEvent(self, event)

         .. code-block:: none

            - Handles window close events.
            - Stops the robot gracefully when the UI is closed.

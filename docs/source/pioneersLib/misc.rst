Miscellaneous Utility Classes
=========================

This portion of documentation contains a collection of utility classes that are used in various parts of the codebase. 
These classes are not specific to any particular functionality but provide general-purpose utilities that can be used throughout a robot project, 
many are not currently used in any active projects but are useful to have around. Classes marked for deprecation or that should soon be marked for deprecation
are not included.

Control Constants
-----------------
Control constants are meant to be ways to encapsulate the values used for FRC control loops (PID, feed forward, etc.) in a single class.
This allows for easy access to the values and makes it easier to change them in one place if needed while also allowing for easy seperation of real and simulated values, but is not optimal for on the fly tuning 
as it requires a recompile to change the values.
Multiple of these classes exist most are simple records like this:

.. code-block:: java
    public record FFCoefficients(
            double kS,
            double kV,
            double kA,
            double kG
    ) {}

Recently however, we have shifted away from using these control coefficients and started using suppliers to differentiate between real and simulated values:

.. code-block:: java
    public static final Supplier<PIDController> PID_CONTROLLER = () ->
		switch (ROBOT_MODE) {
			case REAL -> new PIDController(5, 0, 0);
			case SIM -> new PIDController(5, 0, 0);
			default -> new PIDController(1, 0, 0);
		};


Tracer
-----------------

The tracer class presents an easy way to profile code, it was originally made from Bowan on team 3173 and profiles each function you attatch to it. There are alternatives to 
using a tracer and making modifications to code such as `VisualVM <https://visualvm.github.io/>`_ but this is a reasonable, simple, and effective way to profile code! It also lets you 
log data during matches so if you have any significant loop overuns you can easily find the culprit.

In order to trace a function simply do:

.. code-block:: java
    Tracer.traceFunc("My Function", myFunction());

The functions time will automatically be logged with AdvantageKit and put to NT.

Crash Check
-----------------

The crash check is part of our comprehensive CI pipeline. It is a class that acts in place of the robot class and manually executes each periodic, entrance, and exit function for
the various robot modes (teleop, autonomous, disabled, etc.). The CI pipeline runs the robot code in a docker container and if the robot code crashes with this class acting as the "robot"
the check will fail. Much more information on this can be found in the CI documentation.

.. TODO: 
    - Add more information on the crash check in a seperate CI section that describes all of our main CI checks!
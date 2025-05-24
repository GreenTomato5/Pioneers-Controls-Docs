YAGSL
=============

YAGSL is a swerve library written by Nstrike, and it is thoroughly documented `here <https://docs.yagsl.com/>`_. The main goal of the library was
to serve as a drop in replacement for teams (often messy) in house swerve code and lower the bar across the board for teams trying to use swerve. 
It has recently expanded to include various other features such as Maple Sim (a high fidelity physics sim), photon vision and limelight templates, and integration with various 
path planning libraries.

The generall functioning of the library is as follows:

1. Generate a series of Json config files based on your physical robot
2. Drop in the Json files, copy paste ~50 lines of Java
3. Run the robot

The process is very simple and the libraries rather streamlined but there are still some pretty significant issues. 

**Benefits:**

- The library is easy to use and the api is begineer friendly
- The library is well documented and has a lot of examples with exterior resources
- The library allows for lots of customization and interoperability between vendors

**Drawbacks:**

- The project is generally suffering from scope creep, for new students being exposed to a variety of rather unecessary features can be overwhelming
- The library is not maintained by a vendor directly, in turn there are not as many optmizations (specifically compared to CTRE swerve) that actually improve performance
- Your entire robot configuration and PID constants are in a Json files, which is not bad persay but can be very difficult to manage
- The abstraction that allows for interoperability obfuscates objects so you have to cast a "motor controller" to whatever vendors controller you're using if you want to directly interact with it.

For new teams, YAGSL is a great place to start, and in 2025 we used it for 7917s drivetrain. You can succeed a lot with the library and it 
will almost always work.
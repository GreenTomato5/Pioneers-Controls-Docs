Swerve
==============

Having a swerve drive lets us easily navigate around the field and gives us a massive advantage over teams without swerve drive. Because of
how important it can be to success in FRC, many vendors have made swerve modules with different gearings, designs, and motor requirements that we and other teams can use. 
With such variability there comes a software problem, how do you program it effectivley? The answer for most teams, including us, is outsource the work to
the vendors that make swerve modules. There are also however other methods that increase interoperability between vendor devices (motors, encoders, etc.) and some distinct
benefits that come with writing your own swerve! In this portion of the docs we will explore different swerve drive libraries, what has been effctive for us, and what
is probably the "best" for the team to continue using.

Lots of the information surrounding these swerve libraries was obtained in the 2025 offseason. Our team did a custom swerve write and tested
3 different swerve libraries before settling on one final solution.

.. note:: The benefits of specifically CTRE swerve and their ecosystem (locks you into their products) wont be as strong once Systemcore comes out because of its onboard CAN FD bus and higher processing capabilities (will likley make the CANIVORE irrelevant). 

What Does WPILIB Provide?
---------

Most of the vendor writen swerve libraries rely heavily on WPILIBs swerve library. For a comprehensive understanding it is best to look at the WPILIB documentation. But For
a brief overview, you initially create a chassis speed object which can be relative to the field (forward is forward relative to the driver) or robot relative (forward is forward relative to the robot). From these
chassis speeds objects you can derive setpoints for your swerve modules with some pretty simple inverse kinematics! There are of course various other smoothening optimizations (setpoint
discretization, cosine compensation, etc.) but that covers the broad overview. We will dive into vendor specific optimizations later on!

Because almost all of these swerve libraries fundamentally rely on the same WPILIB API you won't really see significant differences in performance 
(at least not game changing ones). The main difference come from maintainability, ease of use, and documentation which some libraries do much better\
than others.

Existing Solutions
-------------

.. toctree::
   :maxdepth: 2

   YAGSL
   advantageKitTemplate
   CTRESwerve
   customSwerve
   whatShouldYouUse


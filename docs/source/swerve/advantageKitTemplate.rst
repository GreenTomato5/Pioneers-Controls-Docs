AdvantageKit Swerve Templates
====================

The advantage kit swerve template was developed by FRC team 6328. It is no longer maintained nor recommended for use
and the new templates do not offer as much interoperability with IO layers but rather seperate into vendor specific libraries.
The new templates can be found `here <https://docs.advantagekit.org/category/template-projects>`_. 

The original template specifically leveraged high frequency odometry, that is running odometry at 250hz instead of the standard 50hz of
the robot periodic. This theoretically (and in practice as tested by 6328) allows for more accurate wheel odometry. It's debateable if this is much
of a help because vision measurments will consistently be added regardless so any significant drift from slower odometry would be corrected to
the same degree as that of higher frequecy odometry.

We initially explored this template as an offseason option for 7525 as a way to teach students and prepare them for an offeason comp! The main issue 
we found was the lack of interoperability by default (the template does not support mixing and matching vendors, especially for high frequency odometry).
This is because CTRE's api uses blocking to get data from the motor controllers while REV uses polling. So, when trying to combine both sampling types in an odometry thread you
have to wait for samples to come in from CTRES blocking data while REV polls data that may not be updated. In turn we had to develop a `hybrid odometry thread <https://github.com/FRC-7525/2024-Rewrite/blob/main/src/main/java/frc/robot/subsystems/drive/HybridOdometryThread.java>`_ that
combined both methods to produce reasonable odometry estimates at about 250hz on the rio. 

Unfortunatley, because of a lack of thorough testing we were plagued by gyro issues at comp and had to revert to our regular in season code 
to win the competition. The template still held up well but we certainly needed more testing.

.. note:: The template is no longer maintained and should not be used for new projects. It is still a good reference for how to implement high frequency odometry and the general structure of a swerve drive codebase.

**Benefits:**

- The template is not black boxed, you have access to everything unlike almost every other lib.
- The template makes use of 250 hz odometry

**Drawbacks:**

- No out of the box interoperability
- The template is no longer maintained
- Not maintained by a vendor
- The template is not as well documented as other libraries
- Dubious benefits of 250hz odometry, especially when taking into account vision, excess optimizations may not be worth the hasstle
Setting Up Repulsor
=================================

Start off by creating a RepulsorFieldPlanner.
.. code-block:: java

   private final RepulsorFieldPlanner repulsor = new RepulsorFieldPlanner(new ArrayList<>(), new ArrayList<>(), (ROBOT_MODE == RobotMode.SIM));

Pass in a empty ArrayList for the obstacles in order to use the default obstacles.
**Make sure to only simulate arrows when you are in simulation mode**

Afterwards, create PID controllers for the x and y axes and rotation.
.. code-block:: java

   private final PIDController xController = new PIDController(0.1, 0, 0);
   private final PIDController yController = new PIDController(0.1, 0, 0);
   private final PIDController rotationController = new PIDController(0.1, 0, 0);

Then you can use the repulsor field planner to get a drive command.

Using repulsor
=================================
In order to use repulsor, you first have to set the goal pose.
.. code-block:: java

   repulsor.setGoal(targetPose.getTranslation());

Afterwards, call the .getCmd() method with the relavant parameters in order to get a swerve sample object.
.. code-block:: java

    SwerveSample sample = repulsor.getCmd(currentPose, drive.getRobotRelativeSpeeds(), MAX_SPEED.in(MetersPerSecond), USE_GOAL, targetPose.getRotation());

Then, extract a chassis speeds object from the swerve sample and modify it by adding outputs from the PID controllers.
.. code-block:: java

    var targetSpeeds = sample.getChassisSpeeds();
		targetSpeeds.vxMetersPerSecond += repulsorTranslationController.calculate(currentPose.getX(), sample.x);
		targetSpeeds.vyMetersPerSecond += repulsorTranslationController.calculate(currentPose.getY(), sample.y);
		targetSpeeds.omegaRadiansPerSecond += repulsorRotationalController.calculate(currentPose.getRotation().getRadians(), sample.heading);

Finally, you can execute your chassis speeds object with your drive subsystem.

.. code-block:: java

    if (Robot.isRedAlliance) {
		drive.driveFieldRelative(-translationVelocity.getX(), -translationVelocity.getY(), thetaVelocity);
	} else {
		drive.driveFieldRelative(translationVelocity.getX(), translationVelocity.getY(), thetaVelocity);
	}
    
.. note::
    The chassisSpeed object is field relative. When passing the values into the drive subsystem, some values may need to be negative depending on the alliance color.
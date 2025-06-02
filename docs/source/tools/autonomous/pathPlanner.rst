PathPlanner
==================
PathPlanner is a graphical tool that helps teams generate autonomous routines for their robots.

Benefits of PathPlanner
------------------
* **Visual Path Creation**: Draw paths directly on a field image.
* **Waypoint Customization**: Define position, heading, and even rotational targets.
* **Event Markers**: Trigger commands (like intake, shoot, etc.) at specific points along the path.
* **Path and Mirroring**: Easily adapt paths for different alliance sides.
* **Multi-Path Sequences**: Chain multiple paths together for complex autonomous modes.

Getting started
----------------
1.  **Installation**:
    PathPlanner is a standalone application. You can download the latest release from their GitHub page.
    * **Official GitHub Releases**: `https://github.com/mjansen4857/PathPlanner/releases <https://github.com/mjansen4857/PathPlanner/releases>`_
.. note:
    To get more information on pathPlanner the docs are available  `here <https://pathplanner.dev/home.html/>`_

2.  **Basics**:
    a. Open PathPlanner and open your project.
    b. There is a settings dialog where you will define some information about yyour robot
    c. Start by tapping the + button and make a new path.
    d. Use the drawing tools to create your path on the field image. (It's recomended to press the optmize button to achieve the best path)
    e. Set Constraints for the path, including max speed, and acceleration.

3. **Combining paths**:
    a. You can create multiple paths and combine them into a auto.
    b. Use the + button and make an auto.
    c. add your paths to the auto ensuring they connect properly.
    d. You can also add events to the auto, which will trigger commands at specific points along the path.
4. **Using in the code**:
    a. Your paths and autos will be saved as json files in the deploy directory.
    b. In your robot code, you can use the `PathPlanner` library to load and execute paths and/or autos.
    c. AutoBuilder is a :hover:`static` class that provides methods to load and execute paths.
    d. It needs to be configured within your robot code, usually in the Drive subsystems constructor.
    .. code-block:: java
    
        RobotConfig config;
        try {
            config = RobotConfig.fromGUISettings();
        } catch (Exception e) {
            // Handle exception as needed
            e.printStackTrace();
        }

        // Configure AutoBuilder last
        AutoBuilder.configure(
                this::getPose, // A method to get robot pose
                this::resetPose, // A method to reset odometry
                this::getRobotRelativeSpeeds, // ChassisSpeeds supplier. MUST BE ROBOT RELATIVE NOT FIELD RELATIVE
                (speeds, feedforwards) -> drive(speeds), // Method that will drive the robot given ROBOT RELATIVE ChassisSpeeds.
                new PPHolonomicDriveController( // PPHolonomicController is the built in path following controller for holonomic drive trains
                        new PIDConstants(5.0, 0.0, 0.0), // Translation PID constants
                        new PIDConstants(5.0, 0.0, 0.0) // Rotation PID constants
                ),
                config, // The robot configuration
                () -> {
                // Boolean supplier that controls when the path will be mirrored for the red alliance
                // This will flip the path being followed to the red side of the field.
                // THE ORIGIN WILL REMAIN ON THE BLUE SIDE

                var alliance = DriverStation.getAlliance();
                if (alliance.isPresent()) {
                    return alliance.get() == DriverStation.Alliance.Red;
                }
                return false;
                },
                this // Reference to this subsystem to set requirements
        );
    .. note::
        We usually build a second manager subsystem for autos ex: AutoManager. This should handle setting up and running the autos.
    e. You can create an auto chooser and push it to smartDashboard as shown:
        .. code-block:: java
            autoChooser = AutoBuilder.buildAutoChooser();

            // Another option that allows you to specify the default auto by its name
            // autoChooser = AutoBuilder.buildAutoChooser("My Default Auto");

            SmartDashboard.putData("Auto Chooser", autoChooser);

            public Command getAutonomousCommand() {
                return autoChooser.getSelected();
            }
    f. You also need to register namedCommands for events in your paths/autos. This MUST be done before creating a PathPlanner path/auto in your code.
        .. note::
            We don't normally use commands so they aren't built into our subsystems. Instead we recommend making a `autoCommands` file and putting commands in there which change subsystems state.
        .. code-block:: java

            // Register named commands for events in your paths
            PathPlanner.registerNamedCommand("Intake", <WhateverCommandIsForIntake>);
            // Add more commands as needed

--------
            

In House Swerve Code
=============

In the offseason of 2025 in addition to testing the 3 other described swerve libraries we also wrote our own. Out library was based
largley on the IO layers present in the advantage kit template and made heavy use of WPILIBs swerve API. The code for our library can be found 
`here <https://github.com/FRC-7525/BumSwerve>`_. The library is not maintained nor thoroughly documented and we advise strongly against 
using it as a drop in replacement but there are certainly things to be learned from it!

Firstly, developing a swerve library was a great way to learn about abstraction and how to structure a codebase for students new to 
FRC programming. We were also able to leverage the benefits we saw in all the other libraries we had tried to make one "better" solution. In testing, we
got to the point where we were able to drive around field relative and run a path planner auto! 

Ultimatley we decided against using the library in competition because of the lack of thorough testing. We wanted to ensure we wouldn't have
any issues with the library at competition and we were confident we could debug any swerve issue with other vendors because we thoroughly understood 
swerve as we had programmed the full library!

**Benefits:**

- The library is not black boxed, you have access to everything unlike almost every other libraries
- By just programming a swerve lib you learn a lot about the internals of all other swerve libraries

**Drawbacks:**

- Not maintained, not well documented, and not a drop in replacement
- Not thoroguhly tested
- Not written by professional software engineers
- Over abstracted

To hammer in the point about over abstracting, here's what an implementation of the library looks:

.. code-block:: java
    public class Drive extends Subsystem<DriveStates> {
    private SwerveDrive drive;
    
    public Drive(SwerveModule[] modules, SwerveGyroIO gyroIO, boolean sim) {
        super("Drive", DriveStates.REGULAR);
        drive = new SwerveDrive(TRACK_WIDTH_X, TRACK_WIDTH_Y, modules, gyroIO, MAX_SPEED, WHEEL_RADIUS, sim, CONTROLLER_DEADBAND);
        switch (Constants.ROBOT_STATE) {
            case REAL:
                drive.configureAnglePID(Real.AZIMUTH_PID.kP, Real.AZIMUTH_PID.kI, Real.AZIMUTH_PID.kD);
                drive.configureDrivePID(Real.DRIVE_PID.kP, Real.DRIVE_PID.kI, Real.DRIVE_PID.kD);
                break;
            case SIM:
                drive.configureAnglePID(Sim.AZIMUTH_PID.kP, Sim.AZIMUTH_PID.kI, Sim.AZIMUTH_PID.kD);
                drive.configureDrivePID(Sim.DRIVE_PID.kP, Sim.DRIVE_PID.kI, Sim.DRIVE_PID.kD);
                break;
            case REPLAY:
                drive.configureAnglePID(0, 0, 0);
                drive.configureDrivePID(0, 0, 0);
                break;
            default:
                break;
        }    
    }

    /**
     * Drive the robot
     * @param x The x supplier for speed (0-1)
     * @param y The y supplier for speed (0-1)
     * @param rot The rotation supplier for speed (0-1)
     * @param fieldRelative Whether the robot should drive field relative
     * @param headingCorrection Whether the robot should correct its heading
     */
    public void drive(DoubleSupplier x, DoubleSupplier y, DoubleSupplier rot, boolean fieldRelative,
            boolean headingCorrection) {
        drive.drive(x, y, rot, fieldRelative, false);
    }

    /**
     * Run drive.period, should be called in robot.periodic
     */
    @Override
    public void runState() {
        drive.periodic();
    }

    public Pose2d getPose() {
        return drive.getRobotPose();
    }

    public void addVisionMeasurment(Pose2d visionPose,
            double timestamp,
            Matrix<N3, N1> visionMeasurementStdDevs) {
        drive.addVisionMeasurement(visionPose, timestamp, visionMeasurementStdDevs);
    }
}

That is everything you needed in our library to drive around the robot, add in odometry measurments, etc.
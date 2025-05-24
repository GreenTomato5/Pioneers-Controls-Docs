Example Subsystem
=====================

Now its time to put together the trigger and subsystem abstractions to make a simple subsystem! This will focus on
making a simple Shooter with only a couple states.

First we need an outline of the subsystem states, what the shooter can do. In this case we have two states, IDLE and SHOOTING.

.. code-block:: java

    public Enum ShooterStates implements SubsystemStates {
        IDLE("Idle", 0.0),
        SHOOTING("shooting", 5000.0),
    }

    String stateString;
    double velocityRPM;

    // Each state needs a string defining it and an associated setpoint for the motor
    ShooterStates(String stateString, double velocityRPM) {
        this.stateString = stateString;
        this.velocityRPM = velocityRPM;
    }

    @Override
    public String getStateString() {
        return stateString;
    }

    public double getTargetVelocityRPM() {
        return velocityRPM;
    }

Idle will represent the state of the robot when the shooter is not running, and it can be assumed that instantly entering the SHOOTING state
will properly score a gamepiece for the sake of simplicity.

Next we need to create the subsystem class itself:

.. code-block:: java

    public class Shooter extends Subsystem<ShooterStates> {
        // We typically use CTRE supplied motors on our robots, but this could be any motor controller
        private final TalonFX motor;
        // We use a PID controller in this example but BangBang, pure FF, or PIDF controllers are all valid options
        private final PIDController pidController;
        private final XboxController controller;

        public Shooter() {
            super("Shooter", ShooterStates.IDLE);
            motor = new TalonFX(...);
            controller = new XboxController(...);
            pidController = new PIDController(...);

            // This encapsulates all state transitions, when the you press a button the robot shoots out a gamepiece and then stops after 1 second 
            addTrigger(ShooterStates.IDLE, ShooterStates.SHOOTING, controller::getAButtonPressed);
            addTrigger(ShooterStates.SHOOTING, ShooterStates.IDLE, () -> getStateTime() > 1.0)

        }

        @Override
        public void runState() {
            double targetVelocity = getState().getVelocityRPM();
            double output = pidController.calculate(motor.getSelectedSensorVelocity(), targetVelocity);
            motor.set(ControlMode.Velocity, output);
            Logger.recordOutput("Shooter/State", getState().getStateString());
        }   

        @Override
        public void stateInit() {
            // Here we ensure the PID controller will be reset to the current velocity, and ignore accumulated error in the IDLE state
            pidController.reset(currentVelocity);
        }
    }

That's it! Now you have a working shooter. This same famework can be easily upscaled to control multiple subsystems and
orchestrate their movement together. 



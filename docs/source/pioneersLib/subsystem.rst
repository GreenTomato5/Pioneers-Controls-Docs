Subsystem and Subsystem State abstractions
========================================================

All discussed file can be found `here <https://github.com/FRC-7525/PioneersLib>`_. 

The subsystem and subsystem state abstractions are used to describe the robot and are core to the library. Both work together
to define all the posible positions of a mechanism and allow us to manage each mechanism individually or as a group.

Subsystem 
------------

Here is the first portion of the Subsystem abstract class, it lays out the needed variables for storage, and defines a subsystem in its constructor.

.. code-block:: java

    public abstract class Subsystem<StateType extends SubsystemStates> extends SubsystemBase {

	private Map<StateType, ArrayList<Trigger<StateType>>> triggerMap = new HashMap<>();
	private List<RunnableTrigger> runnableTriggerList = new ArrayList<>();

	private StateType state = null;
	private Timer stateTimer = new Timer();
	private String subsystemName;

	public Subsystem(String subsystemName, StateType defaultState) {
		if (defaultState == null) {
			throw new RuntimeException("Default state cannot be null!");
		}
		this.subsystemName = subsystemName;
		this.state = defaultState;

		stateTimer.start();
	}

You will notice 2 arguments needed when creating a subsystem you must extend this abstract subsystem class and call the super constructor, passing in a name for the subsystem and 
its starting state for logging and initialization. Typically you want your default state to be your idling state, allowing you to return to a "safe" origin if need be. You will also notice
the generic brackets (<?>) in the class definition and an extension of subsystem base. This is because the subsystem class is a generic abstract class, and the type of state it will be using is defined in the brackets.
So when subclassing this abstract class you must specify the type of state it will be using.

.. note:: The Subsystem class currently extends the Subsystem Base class, this class is not internal to the team and is part of WPILIB. We extend this class to act as a shim for tools such as SysID, pathPlanner, and others that require a class that derives from SubsystemBase to function properly. Ensure you call "CommandScheduler.getInstance().unregisterAllSubsystems();" to avoid calling subsystem periodics twice as a result of subclassing.

Moving into the meat of the class, we have a trigger map and runabble trigger list. The conditions for runnable triggers are checked and if met the subsequent
runable is executed. If the subsystem is in the correct state and the condition for a regular trigger is met the state will switch. This is all defined in the periodic function
of the subsystem which the user is **NOT** meant to override, rather they should override the runState function to describe what will be called periodically. In order to define subsystem behavior 
when entering or exiting states, there are also "state init" and "state exit" functions. The former is called imediatley after a state transition occurs and the latter immediatley after. This can be particularly useful for
resetting PID controllers, booleans, etc.

.. code-block:: java

    // State operation
	public void periodic() {
		// Commented out bc bad code
		// Logger.recordOutput(subsystemName + "/state", state.getStateString());
		// if (!DriverStation.isEnabled()) return;
		
		runState();

		checkTriggers();
		checkRunnableTriggers();
	}

	protected abstract void runState();
	
	/**
	 * Called AFTER the subsystem is set to a new state.
	 * Override to implement functionality
	 */
	protected void stateInit() {};

	/**
	 * Called BEFORE the subsystem is set to a new state. 
	 * Override to implement functionality
	 */
	protected void stateExit() {};

	/**
	 * Triggers for state transitions
	 * @param startType The {@link StateType} for starting
	 * @param endType The {@link StateType} for ending
	 * @param condition A {@link BooleanSupplier} that triggers the state transition
	 */
	protected void addTrigger(StateType startType, StateType endType, BooleanSupplier condition) {
		triggerMap.computeIfAbsent(startType, k -> new ArrayList<>())
				.add(new Trigger<>(condition, endType));
	}

	protected void addRunnableTrigger(Runnable runnable, BooleanSupplier check) {
		runnableTriggerList.add(new RunnableTrigger(check, runnable));
	}

	private void checkTriggers() {
		List<Trigger<StateType>> triggers = triggerMap.get(state);
		if (triggers == null) return;

		for (var trigger: triggers) {
			if (trigger.isTriggered()) {
				setState(trigger.getResultState());
			}
		}
	}

	private void checkRunnableTriggers() {
		for (var trigger: runnableTriggerList) {
			if (trigger.isTriggered()) {
				trigger.run();
			}
		}
	}


There are also various utility functions in the subsystem class. They allow you to get SysID commands, time you've been in the current active state, the current state, and more:

.. code-block:: java

    public StateType getState() {
		return state;
	}

	public void setState(StateType state) {
		if (this.state == state) return;

		stateTimer.reset();
		stateExit();

		this.state = state;
		stateInit();
	}

	/**
	 * Gets amount of time the state machine has been in the current state.
	 *
	 * @return time in seconds.
	 */
	protected double getStateTime() {
		return stateTimer.get();
	}

	public Command sysIdDynamic(Direction direction) {
		return new PrintCommand("Please Override Me!");
	}

	public Command sysIdQuasistatic(Direction direction) {
		return new PrintCommand("Please Override Me!");
	}
}



Subsystem States
------------

.. note:: This section refers to SubsystemStates.java not SubsystemState.java. The latter is an abstraction meant to simplify states but we determined that it was uneeded and reduced readability.

In order to define the subsystem state type that each subsystem requires, there is a specific interface that each subsystem state enum must inherit from. It is very short and simple:

.. code-block:: java
    
    public interface SubsystemStates {
        default String getStateString() {
            throw new UnsupportedOperationException(
                "This method must be overridden by the implementing class"
            );
        }
    }

Essentially, any enum that inherits from this interface is required to have a getStateString function. This makes logging easy as we can just get the state string associated with the
current active state of any subsystem and log it! We determined that requiring other functions would be too constraining because there is so much variability among subsystems.
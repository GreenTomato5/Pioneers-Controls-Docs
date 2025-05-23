Triggers
=================

Triggers are objects used to define state transitions, that is they are used to define when the state of the robot should change. We also have
RunnableTriggers which are made specifically for executing small blocks of code when a condition is met in a way that better matches the overall
paradigm of the library.

State Transition Triggers
-----------------------------

.. note:: The current name for state transition triggers is not particularly descriptive, they will likley be changed from "Trigger" to "StateTransitionTrigger" in the future.

State transition triggers are used to define when a state transition should occur. They are defined with a condition that is checked to see if the trigger is "triggered" and a result state that is set when the trigger is triggered. Only 
triggers with a starting state that is the same as the current active state are checked in the periodic loop. 

.. code-block:: java

    public class Trigger<StateType extends SubsystemStates> {
        BooleanSupplier supplier;
        StateType state;

        public Trigger(BooleanSupplier supplier, StateType state) {
            this.supplier = supplier;
            this.state = state;
        }

        public boolean isTriggered() {
            return supplier.getAsBoolean();
        }

        public StateType getResultState() {
            return state;
        }
    }

The class is generic, meaning you can use it with any type of state. This is useful because it allows you to define triggers for any state in your robot's state machine including non explicitly managerial subsystems.


Runnable Triggers
-----------------------------

Runnable triggers have two rather simple arguments, a runnable to be executed and a BooleanSupplier that is used to check if the runnable should be executed.

.. code-block:: java 

    public class RunnableTrigger {
        BooleanSupplier supplier;
        Runnable runnable;

        public RunnableTrigger(BooleanSupplier supplier, Runnable runnable) {
            this.supplier = supplier;
            this.runnable = runnable;
        }

        public boolean isTriggered() {
            return supplier.getAsBoolean();
        }

        public void run() {
            runnable.run();
        }
    }

The execution of the runnable, checking the "isTriggered" function and all related logic is handled in the subsystem class as described in the subsystem portion of
these docs. While at first these may seem unintuitive (they essentially abstract away an if statement) having them has proved to be very useful in improving readability in
cases where we have many inputs that need to be check. For example in order to control our robot in 2025 we had a button board to select scoring location with 6 distrinct locations, it would be much simpler to have
runnable triggers such as:

.. code-block:: java

    addRunnableTrigger(() -> {
        // Do something
    }, () -> buttonBoard.getButton(0));
    addRunnableTrigger(() -> {
        // Do something
    }, () -> buttonBoard.getButton(1));
    addRunnableTrigger(() -> {
        // Do something
    }, () -> buttonBoard.getButton(2));
    ...

When compared to

.. code-block:: java
    
    if (buttonBoard.getButton(0)) {
        // Do something
    } else if (buttonBoard.getButton(1)) {
        // Do something
    } else if (buttonBoard.getButton(2)) {
        // Do something
    }

This is also opinionated! If you don't like it, don't use it just decide on a standard! It is not a requirement of the library and you can simply use the if statements as you would in any other codebase.
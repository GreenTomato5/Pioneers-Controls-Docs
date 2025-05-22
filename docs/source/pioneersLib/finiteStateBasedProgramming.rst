State Based Timed Robot
=========================================

.. note:: There are many ways to write state machines, this is just one of them.

Our team interestingly does not use the standard WPILIB Command Based programming architecture (link). It is very important that
everyone understands exactly why we do this and that both approaches are acceptable.

There are two main goals whenever programming a robot:
1. Make the robot do what you want it to do, have a functional machine
2. Make the code easy to read and understand, have a maintainable codebase

The first goal is the most important, and the second goal is a close second. The first goal is what we are trying to achieve with
the state machine. The second goal is what we are trying to achieve with the state machine and the way we write it. 

The state machine is a finite state machine (FSM) that is used to control the robot. It is a model of computation that consists of a 
finite number of states, transitions between those states, and actions. The FSM is used to control the robot by defining the states that 
the robot can be in, the transitions between those states, and the actions that are taken when the robandaot is in a particular state. Command
programming is very similar if not the same in principle, and the only difference between our implementation and their is how we define
transitions and states.

Command based states associate what are essentially Runnables with a given trigger, when a trigger is triggered that runnable is executed
or the command is scheduled. This means you can associate a given Runnable with the state of each subsystem on the robot and then compose
these runnables together to create a robot wide command that controls the entire system. 

This API is essentially a drop in replacement for state machines. In industry and oftentimes in FRC before the Command based framework
state machines were essenetially massive enums and a if statement blocks that checked the current state of each mechanism
and executed code based on that state. This pattern was often messy, teams did not have a standard way of programming robots,
and in turn code was hard to share, it wasn't very externally readable, and often wasn't maintainable for teams.

So why in the world don't we use the command based framework? The answer is rather simple, it doesen't provide the same opportunities
for students by nature. Students don't design and build their own framework with their prefered abstractions if they use command based,
they are forced to use an established rigid framework. Because of the knowledge we have accumulated on the team maintaining our own 
library for composing such state machines has been sustainable and easy to do. Students can easily edit the library and modify it to
their liking. We don't have issues with not intimatley understanding issues related to the framework used to run our robot because we made the
framework ourselves and it is much less verbose and rigid than the command based framework. 
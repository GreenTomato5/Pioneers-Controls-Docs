Autonomous
===================

Our autonomous approach
-----------------------------

The autonomous period is a crucial part of every FRC match. During this period, robots operate without any driver input, executing pre-programmed routines to score points. This period can significantly impact a team's performance.

WPILib Commands in Autonomous
-----------------------------

Our autonomous code is the *only* place in our entire codebase where we utilize `WPILib Commands <https://docs.wpilib.org/en/stable/docs/software/commandbased/index.html>`_. This design choice makes a lot of sense for several reasons:

* **Sequential Execution:** The command-based programming model in WPILib is inherently designed for orchestrating sequences of actions, which is exactly what an autonomous routine is. We can easily define actions that run sequentially (one after another) or in parallel (at the same time), allowing for complex and efficient autonomous routines.
* **Interruptibility and State Management:** Commands handle their own state and can be interrupted, which is valuable for robust autonomous programming. If something goes wrong or a new condition is met, commands can be stopped and new ones started.
* **Readability and Organization:** Breaking down an autonomous routine into individual commands (e.g., ``DriveToSpeakerCommand``, ``ShootNoteCommand``) makes the code much more readable and easier to maintain. Each command encapsulates a specific robot behavior.

Graphical Path Generation: PathPlanner and Choreo
-------------------------------------------------

To simplify the creation of complex driving paths, we leverage powerful GUI-based tools like `PathPlanner <pathPlanner.html>`_ and `Choreo <choreo.html>`_. These tools allow us to:

* **Visually Draw Paths:** Instead of manually calculating trajectories and waypoints in code, we can graphically draw out the desired paths on a field diagram. This greatly reduces development time and allows for precise path tuning.
* **Combine Paths with Actions:** Both PathPlanner and Choreo allow us to integrate custom actions (our WPILib Commands) at specific points along the generated path. For example, we can specify that the robot should deploy its intake at a certain waypoint, or shoot a note when it reaches a specific position. This allows us to build complete autonomous routines that combine movement with manipulation.
* **Motion Profiling:** These tools generate motion profiles that account for the robot's kinematics and dynamics, resulting in smoother and more accurate autonomous movements.

Repulsor: Coordinate-Based Autonomous
-------------------------------------

Recently, we've begun experimenting with `Repulsor <repulsor.html>`_. as an alternative method for autonomous travel. Repulsor offers significant advantages in terms of flexibility and rapid auto generation:

* **Greater Flexibility:** Unlike pre-drawn paths, Repulsor allows us to define autonomous movements simply by providing target coordinates. This is good for scenarios where the exact path might vary or where adjustments are needed.
* **Faster Auto Generation:** Instead of drawing and refining paths, we can generate autonomous routines much more quickly by simply specifying a sequence of coordinates the robot needs to reach. This is particularly useful for adapting to different game strategies.

.. note::
   It is technically possible to create autonomous routines using a timer, where the robot performs different actions at specific time intervals (e.g., "drive forward for 2 seconds, then turn left for 1 second"). However, this approach is **highly discouraged** and **should not be done**.

Autonomous Tools
-----------------------------

.. toctree::
   :maxdepth: 1

   choreo
   pathPlanner
   repulsor
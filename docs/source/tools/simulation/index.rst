Simulation
==============
We use simulation to test our code in a virtual environment before deploying it to the robot. This allows us to catch bugs and ensure our code behaves as expected without risking damage to the physical robot or our syurroundings.

.. note:: 
   Simulation is not a replacement for testing on the actual robot, but it is a valuable tool for development and debugging.


As a team, we primarily use two tools for simulation:
- **WPILibSimGUI**: This is built into WPILib and provides a 2d simple GUI for simulating robot code. It is easy to setup but limited in functionality.
- **AdvantageScope**: This is a more advanced tool and it provides a 3D enviroment with the ability to upload custom robot models. It is built as an extension to WPILib sim.

.. toctree::
   :maxdepth: 1

   advantageScope
   simGUI
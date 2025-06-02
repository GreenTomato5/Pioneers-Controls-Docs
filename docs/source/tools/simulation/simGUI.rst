WPILib simGUI
========================
WPILib simGUI is a simple 2D simulation tool that comes with WPILib. It allows you to visualize your robot's movements and test your code in a simulated environment. This is useful for debugging and testing before deploying code to the actual robot.
It is not as feature-rich as AdvantageScope, but it is easy to set up and use.

Setting Up WPILib simGUI
-----------------------------
This is built into WPILib so all you have to do is type ctrl+shift+P and type simulate. Select the "Simulate Robot Code" option. Then select "sim GUI". 

.. note::
    If you want a better simulation experience, we recommend using the following sim configuration:
    - Go to `this file <https://raw.githubusercontent.com/FRC-7525/Reefscape-7917/refs/heads/main/misc/ControlsStuff/ultimate-sim-configs.json>`_.
    - Copy the contents of the file and paste them into the simgui-window.json file in your project directory.

How to Use WPILib simGUI
-----------------------------
This simulation will show everything you log to NT from your robot code.
It also has a 2d field view that shows the robot's position on the field.
You can select keyboards or controllers to control the robot in the simulation.

-----------------------
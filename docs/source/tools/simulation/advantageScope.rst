Advantage Scope
===============

Advantage scope is a very powerful simulation tool that allows you to visualize mechanisms
in 3d (given you have a CAD model of the robot). In addition it offers a variety of graphing, odometry, swerve, and other 
visualization features. It's essentially the ultimate progrmaming dashboard!

These docs will mainly focus on how to configure the 3d simulation as the other features are well documented in the 
official documentation: https://docs.advantagescope.org/.

How To Setup Your Simulation
----------------------------

.. note:: 3D Simulation is only useful for visualiznig mechanisms that pivot. Flywheels and things of the sort are not easy to visualize! You can still simulate everything though.
Before this guide begins you will have to do a couple things:

    - Install Advantage Scope: https://github.com/Mechanical-Advantage/AdvantageScope 
    - Install CAD Assistant: https://www.opencascade.com/products/cad-assistant/

**How to Simulate Components:**

1. Download each component you want to simulate as an individual CAD file separated from the main robot as a STEP file. 
    
    - The main robot should also be downloaded as a STEP file but remove the components you are simulating from that assembly (do this all in Onshape).
    - So if you were simulating the AlgaeBar on the Frontier bot you would have 2 files (AlgaeBar and Robot)

2. Use CAD assistant to convert each STEP file into glTF format
    
    - Follow this naming convention “model.glb” for the robot and “model_0.glb” -> “model_1.glb” -> etc.. for the components. For example:

        a. model.glb = the main robot CAD file  
        b. model_0.glb = Your first mechanism, lets say a pivot
        c. model_1.glb = Your second mechanism, lets say a climber


.. note:: If someone has already goten the files, download them and skip past 1-2


3. Open AdvantageScope and navigate to the custom assets folder under help -> show assets folder

4. Create a folder named “Robot_” + whatever you would like the robot to be named in advantage scope. Eg: Robot_Pothole or Robot_Speedbump
Drop the CAD files into this folder, make sure they still follow that naming convention
Your folder should now look like this:

````
    AdvantageScope
    └── assets
        └── custom
            └── Robot_Pothole
                ├── model.glb
                ├── model_0.glb
                └── model_1.glb
````
5. Create a JSON file named “config.json” in the same folder. The following should go in this file. Feel free to replace the values with ones that work for your own robot (name, link, etc.)
.. note:: You do not need to change values in this JSON until you can see the robot in the simulation (see later steps) this is basically just a template.

.. code-block:: json
        
    {
        "name": "<RobotName>",
        "sourceUrl": "<SourceURL>",
        "rotations": [
        {
            "axis": "x",
            "degrees": 90
        },
        {
            "axis": "y",
            "degrees": 0
        },
        {
            "axis": "z",
            "degrees": 90
        }
        ],
        "position": [
        0,
        0,
        0,
        ],
        "cameras": [],
        "components": [
        {
            "zeroedRotations": [
            {
                "axis": "x",
                "degrees": 90
            },
            {
                "axis": "z",
                "degrees": 90
            },
            {
                "axis": "y",
                "degrees": 0
            }    
            ],
            "zeroedPosition": [
            0,
            0,
            0
            ]
        }
        ]
    }

6. Change the name field to the folder ending, so Robot_(name) where name is what you will fill in for the name field in the json. Also make the number of components in the components array equal the amount of components you have for your robot.

7. Start WPILib sim and open AdvantageScope. Then go to file -> connect to sim. Ensure your sim is logging a pose2d for the robot at (0, 0)
    - Find this pose in the left sidebar and drag it out.
    - Click on the icon on the left of the pose2d name and select your robot model.
    - Change your field to the axes field and you should see your robot
    - If you do not see your robot, check the console for errors and ensure your JSON is correct.
8. If your robot is not in the correct orientation, you will need to change the rotations in the JSON file.
    - The degrees are in standard degrees (0-360) and can be negative for counter-clockwise rotation.
9. Once your robot is in the correct orientation, you can now add components to the robot. You should be logging a pose 3d for each component which for now can be (0,0,0) with 0 rotation arround all axis.    
    .. code-block:: java

        public Pose3d intakePose = new Pose3d(new Translation3d(0, 0, 0), new Rotation3d(0, 0, 0));
    - Drag these poses from the sidebar and drop them inside of your robot in the poses bar.
    - Ensure the component poses are named in the same order as your components in the JSON file.
    - The components rotations should be the same as the robot's rotations, so if your robot is rotated 90 degrees around the x-axis, your component should also be rotated 90 degrees around the x-axis.
10. Now using trial and error adjust the zeroed position in the JSON file for each component to move it to the origin.
    - you want the spot the component pivots arround to be aligned with the axis of rotation you want it to pivot around.
11. Next you want to ge the opposite of the zeroed translation (NOT ROTATION) for each component and put that in your pose3d for the component in your code.
    - For example, if your intake pose is (-0.3, 0, 0.1) in the JSON file, you want to set your intake pose in code to (0.3, 0, -0.1).
    - This is because the robot pose will automatically apply the translation to the component so you only need to log the rotation of the component.
12. Now ensure your robot code logs the rotation happening to the component.
.. note:: The actual updated robot pose will automatically be applied to each component so if you drive forward and the robot moves the components will move along with it with nothing extra required

-----------------
Advantage Advantage Scope
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

How to Simulate Components:

1. Download each component you want to simulate as an individual CAD file separated from the main robot as a STEP file. 
    
    - The main robot should also be downloaded as a STEP file but remove the components you are simulating from that assembly (do this all in Onshape).
    - So if you were simulating the Amp Bar and Intake from 2024 you would have 3 files (Intake, Amp Bar, and Robot)

2. Use CAD assistant to convert each STEP file into glTF format
    
    - Follow this naming convention “model.glb” for the robot and “model_0.glb” -> “model_1.glb” -> etc.. for the components. For example:

        a. model.glb = the main robot CAD file  
        b. Model_0.glb = Your first mechanism, lets say a pivot
        c. model_1 .glb = Your second mechanism, lets say a climber


.. note:: If someone has already goten the files, download them and skip past 1-2


3. Navigate to the custom assets folder under help -> show assets folder

4. Create a folder named “Robot_” + whatever you would like the robot to be named in advantage scope. I named mine “Robot_Brysala” because Brysala was the name of our robot this year
Drop the CAD files into this folder, make sure they still follow that naming convention
Your folder should now look like this:

5. Create a JSON file named “config.json” in the same folder. The following should go in this file. Feel free to replace the values with ones that work for your own robot (name, link, etc.). For the first rotation and translation field **edit the json until your robot is properly aligned with the origin in advantage scope**.
.. note:: You do not need to change values in this JSON until you can see the robot in the simulation (see later steps) this is basically just a template.

.. code-block:: json
        
    {
        "name": "Brysala",
        "sourceUrl": "https://cad.onshape.com/documents/2ad45fd2d07b25004d4a1b69/w/43e98844b0278e0c2f1d182a/e/f5d325306461f68e6a7bb5c5?renderMode=0&uiState=6674cpc65b74237201daf6380",
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
        0.045
        ],
        "cameras": [


        ],
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
            -0.31,
            0,
            -0.195
            ]
        },
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
            0.317,
            0.0,
            -0.48
            ]
        }
        ]
    }




6. Change the name field to the folder ending, so Robot_(name) where name is what you will fill in for the name field in the json

7. By default each component and robot will appear in the position it is in in the cad file (in the orientation of the robot). However, the rotation will not be initially correct without chaging the position of the components

    - The “zeroed rotation” correctly orients each mechanism and robot in the XYZ simulation plane (Makes sure everything is upright and facing the same direction)
    - The “Zeroed positions” translate each mechanism to the axis it will rotate around (See each axis by creating a new field 3d tab and changing the field to “Axes”, red is roll, yaw is blue, pitch is green). Because in our pose 3ds we manipulate pitch, the components are oriented to the green axis.
    - Really the components should be zeroed at the origin so you can rotate them in any direction but I didn’t do that in this walkthrough because I was lazy
    - If you wanted to try updating the zeroed poses yourself simply editing the JSON and then saving it will automatically update the sim if you have the poses selected for correlating to each component (More on that in a second). Re-center each component with their respective poses

8. You want the actual pose (what you visualize) to rotate around the correct axis but also actually be on the robot.
    - To do this you just have to apply the transformations NOT THE ROTATIONS applied to the mechanisms and robot in reverse
    - If the robot was shifted -4 units in the x direction and your component was shifted -3 units in the x direction you would reverse that transformation back to its initial pose by adding 7 to the X in the pose 3d for that component
        a. So new Pose3d(new Translation3d(0, 0, 0), new Rotation3d(0, io.getPosition(), 0))); would turn into new Pose3d(new Translation3d(7, 0, 0), new Rotation3d(0, io.getPosition(), 0)));
    - Do the same for X, Y, and Z 

.. note:: The actual updated robot pose will automatically be applied to each component so if you drive forward and the robot moves the components will move along with it with nothing extra required

9. It's time to simulate the robot!

    - Run the sim (same deal as turn tunning) ctrl + shift + p -> simulate robot code
    - In the top left of Advantage Scope click file -> connect to sim and it should automatically connect to the sim. You can verify this by looking at the left side bar, if logged values show up your code is running!
    - In the top right of Advantage Scope click the plus button and then 3D Field to add a new field tab
    - In the bottom right of the field tab to the right of the robot select your robot name, so for me, that’s Brysala because I named my folder “Robot_Brysala”
    - Locate “Robot” which should be a pose2d under real outputs (The robot pose) and drag it into the pose2ds column, selecting “Robot” in the white boxn
    - Drag and drop the component pose3ds that you should have already logged into the    pose3d column in the same naming order as your component CAD files
    - This means if intake is your “model_0” file your drag and drop the intake pose3d first and vice versa
    - Your robot should show up in the sim and updating the pose 3d through any button presses should update the sim!

10. Log poses for each of your mechanisms and your robot pose

    - Again if your mechanisms axis of rotation is not zeroed about the field axis you want it to pivot around, your logged pose for the mechanis will not visualize it properly
    - The logged posted should only contain rotation values as the translation is already applied to the mechanism through the robot! (for pivots, if you have an elevator that's a different story)

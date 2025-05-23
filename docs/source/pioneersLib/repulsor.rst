Repulsor Field
==============

@Thien you understand the underlying math it might be best for you to write this

Repulsor field is an effective way for the robot to navigate the field autonomously. Using repulsor or any autonomous software for that
matter requires some level of localization: understanding the position of the robot on the field. Given the position of the robot
on the field we can calculate what is essentially a vector field that can guide the robot to a target given 
obstacles on the field.

Repulsor field is best used in tandem with a simple drive to pose function that relies on a profiled PID controller. You can transition between Using
repulsor field to align to the general location of your target once you have line of sight to the target (@WILLIAM!!!) or once you are within a specified
distance of the target.

(This is very barebones, need images and code blocks describing what everything does)
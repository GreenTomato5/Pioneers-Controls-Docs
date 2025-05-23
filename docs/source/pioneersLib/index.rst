Internal Team Library
==============

.. important:: Vendor dependency versions MUST match between the robot project and the library. If they do not match you are likley to have issues EVEN IF THEY DO NOT APPEAR ON ALL DEVICES. Because of non-deterministic dependency resolution you sim with mismatched dependencies may wowrk on one machine but not another.

We activley maintain an `internal library <github.com/FRC-7525/PioneersLib>`_ for storing useful abstractions that we use from season to season.
Both FRC team 7525 and 7917 make use of the library so setting up a maven repo to make version managment simple was a no brainer. We had attempted
to simply copy paste over a folder full of java files but it was a pain to manage, especially when drawing from code in previous projects.

The library now contains useful utility files, our subsystem base classes, and state machine abstractions!

.. note:: this library draws a lot on the work of other teams, if you add something to the library please give them credit!

Table of Contents
------------------

.. toctree::
   :maxdepth: 2

   finiteStateBasedProgramming
   triggers
   subsystem
   exampleSubsystem
   repulsor
   misc
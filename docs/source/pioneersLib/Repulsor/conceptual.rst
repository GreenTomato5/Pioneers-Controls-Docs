Conceptual Breakdown
========================

At it's most basic form, repulsor field just calculates a bunch of forces that are being applied from obstacles. 
Most obstacles use the same formula for calculating forces with the exception being the guided obstacle. 

.. image:: /pioneersLib/repulsorFormula.png
   :alt: Formula for force applied
   :align: center

The Obstacle Abstract class
---------------------------------
The Obstacle class is the base for all obstacles.
Here is the code for the Obstacle class:

.. code-block:: java

   public abstract static class Obstacle {
       double strength = 1.0;
       boolean positive = true;
      
       public Obstacle(double strength, boolean positive) {
           this.strength = strength;
           this.positive = positive;
       }

       public abstract Force getForceAtPosition(Translation2d position, Translation2d target);

       protected double distToForceMag(double dist) {
           var forceMag = strength / (0.00001 + Math.abs(dist * dist));
           forceMag *= positive ? 1 : -1;
           return forceMag;
       }

       protected double distToForceMag(double dist, double falloff) {
           var original = strength / (0.00001 + Math.abs(dist * dist));
           var falloffMag = strength / (0.00001 + Math.abs(falloff * falloff));
           return Math.max(original - falloffMag, 0) * (positive ? 1 : -1);
       }
   }

   The strength field is used to determine the force scaling of the obstacle.
   Positive is used to determine whether the force should repel or attract. (Positive for repel)

Point Obstacle
-----------------
finish later bc need reworking
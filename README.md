# The C++ Project Write-up #

This is the write-up for the C++ project.

## The Tasks ##

For this project, you will be building a controller in C++.  You will be implementing and tuning this controller in several steps.

You may find it helpful to consult the [Python controller code](https://github.com/udacity/FCND-Controls/blob/solution/controller.py) as a reference when you build out this controller in C++.

#### Notes on Parameter Tuning
1. **Comparison to Python**: Note that the vehicle you'll be controlling in this portion of the project has different parameters than the vehicle that's controlled by the Python code linked to above. **The tuning parameters that work for the Python controller will not work for this controller**

2. **Parameter Ranges**: You can find the vehicle's control parameters in a file called `QuadControlParams.txt`. The default values for these parameters are all too small by a factor of somewhere between about 2X and 4X. So if a parameter has a starting value of 12, it will likely have a value somewhere between 24 and 48 once it's properly tuned.

3. **Parameter Ratios**: In this [one-page document](https://www.overleaf.com/read/bgrkghpggnyc#/61023787/) you can find a derivation of the ratio of velocity proportional gain to position proportional gain for a critically damped double integrator system. The ratio of `kpV / kpP` should be 4.

### Body rate and roll/pitch control (scenario 2) ###

First, you will implement the body rate and roll / pitch control.  For the simulation, you will use `Scenario 2`.  In this scenario, you will see a quad above the origin.  It is created with a small initial rotation speed about its roll axis.  Your controller will need to stabilize the rotational motion and bring the vehicle back to level attitude.

To accomplish this, you will:

1. Implement body rate control

 - implement the code in the function `GenerateMotorCommands()`       
 - implement the code in the function `BodyRateControl()`
 - Tune `kpPQR` in `QuadControlParams.txt` to get the vehicle to stop spinning quickly but not overshoot

2. Implement roll / pitch control

 - implement the code in the function `RollPitchControl()`
 - Tune `kpBank` in `QuadControlParams.txt` to minimize settling time but avoid too much overshoot
 
The `GenerateMotorCommands()` is written on `Lines 73-88` of `QuadControl.cpp`. It converts thrust and moments about the inertial axes received from the controllers into thrust commands for each individual motor. The drone dimensions were accounted for when calculating the moment arm. Motor numbers and rotation directions were also accounted for. Finally, the individual thrust commands for each motor were constrained to the minimum and maximum defined in `QuadControlParams.txt`.

The `BodyRateControl()` is written on `Lines 118-120` of `QuadControl.cpp`. The controller took into account the moments of inertia of the drone and proportianal body angle rate gains `kpPQR = 60, 30, 10` defined in `QuadControlParams.txt`.

The `RollPitchControl()` is written on `Lines 152-185` of `QuadControl.cpp`. The controller was configured to command 0 roll and pitch rates if a negative thrust command is received since this is impossible to command (our quad does not have variable pitch propellers). Additionally the roll and pitch angles are constrained by the maximum tilt angle defined in `QuadControlParams.txt` and the proportional gain was set to `kpBank = 16`.

If successful you should now see the quad level itself (as shown below), though it’ll still be flying away slowly since we’re not controlling velocity/position!  You should also see the vehicle angle (Roll) get controlled to 0.

<p align="center">
<img src="animations/scenario2.gif" width="500"/>
</p>


### Position/velocity and yaw angle control (scenario 3) ###

Next, you will implement the position, altitude and yaw control for your quad.  For the simulation, you will use `Scenario 3`.  This will create 2 identical quads, one offset from its target point (but initialized with yaw = 0) and second offset from target point but yaw = 45 degrees.

 - implement the code in the function `LateralPositionControl()`
 - implement the code in the function `AltitudeControl()`
 - tune parameters `kpPosZ` and `kpPosZ`
 - tune parameters `kpVelXY` and `kpVelZ`
 - implement the code in the function `YawControl()`
 - tune parameters `kpYaw` and the 3rd (z) component of `kpPQR`

The `AltitudeControl()` and `LateralPositionControl()` are written on `Lines 216-226` and `Lines 264-276`, respectively, of `QuadControl.cpp`. The gains defined are 
#### Position control gains ####
kpPosXY = 2.5
kpPosZ = 8
KiPosZ = 10

#### Velocity control gains ####
kpVelXY = 15
kpVelZ = 18

<p align="center">
<img src="animations/scenario3.gif" width="500"/>
</p>
### Non-idealities and robustness (scenario 4) ###

In this part, we will explore some of the non-idealities and robustness of a controller.  For this simulation, we will use `Scenario 4`.  This is a configuration with 3 quads that are all are trying to move one meter forward.  However, this time, these quads are all a bit different:
 - The green quad has its center of mass shifted back
 - The orange vehicle is an ideal quad
 - The red vehicle is heavier than usual

1. Run your controller & parameter set from Step 3.  Do all the quads seem to be moving OK?  If not, try to tweak the controller parameters to work for all 3 (tip: relax the controller).

2. Edit `AltitudeControl()` to add basic integral control to help with the different-mass vehicle.

3. Tune the integral control, and other control parameters until all the quads successfully move properly.  Your drones' motion should look like this:

<p align="center">
<img src="animations/scenario4.gif" width="500"/>
</p>


### Tracking trajectories (scenario 5) ###

Now that we have all the working parts of a controller, you will put it all together and test it's performance once again on a trajectory.  For this simulation, you will use `Scenario 5`.  This scenario has two quadcopters:
 - the orange one is following `traj/FigureEight.txt`
 - the other one is following `traj/FigureEightFF.txt` - for now this is the same trajectory.  

How well is your drone able to follow the trajectory?  It is able to hold to the path fairly well?

With the two different trajectories, your drones' motions should look like this:

<p align="center">
<img src="animations/scenario5.gif" width="500"/>
</p>

### Performance Metrics ###

The specific performance metrics are as follows:

 - scenario 2
   - roll should less than 0.025 radian of nominal for 0.75 seconds (3/4 of the duration of the loop)
   - roll rate should less than 2.5 radian/sec for 0.75 seconds

 - scenario 3
   - X position of both drones should be within 0.1 meters of the target for at least 1.25 seconds
   - Quad2 yaw should be within 0.1 of the target for at least 1 second


 - scenario 4
   - position error for all 3 quads should be less than 0.1 meters for at least 1.5 seconds

 - scenario 5
   - position error of the quad should be less than 0.25 meters for at least 3 seconds

## Authors ##

Thanks to Fotokite for the initial development of the project code and simulator.

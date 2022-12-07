# Cannon Prediction
This module covers cannon prediction for cannonballs and chainshots.

By now, you should understand the basic structure of a module and the use of the update and init functions. This explanation will omit the obvious parts of the code and focus on the new parts. (I wont tell you that you need to update things and initialise things bc you should know that by now)

## What you need
- A ship target actor
- A local ship actor

## How to get them
The way I initialise a prediction display is that when I find a ship in sot_hack, if the ship is within ~15m of me I set it to my local ship, otherwise I append it to a list of ships. after scanning all actors I then iterate over the list of ship targets and create a prediction display for each one given the ship actor address and the local ship actor address.

## Linear Velocity, Angular Velocity and Yaw
Given a ship address, we can get angular velocity, linear velocity and yaw. (these are necessary for predicting the ship's future position).
They are all struct 'fff'

we use rep_movement for everything
- rep_movement_address = address + Actor.ReplicatedMovement

from rep_movement we read_bytes at (rep_movement + RepMovement.LinearVelocity) and (rep_movement + RepMovement.AngularVelocity) to get the linear and angular velocity of the ship, and read_float at (rep_movement + RepMovement.Rotation) to get the yaw of the ship. All of these are size 0xc

after reading the bytes we struct.unpack('fff') them to get the x, y and z values of the velocity and yaw.

We need all of the linear_velocity floats, the third angular_velocity float and the second yaw float.
```python
linear_velocity
angular_velocity[2]
yaw[1]
```

## Known Values
- Projectile Velocity = 57
- Gravity = 9.81
- Scaling for a normal ball = 0.712
- Scaling for a chain shot = 1

## What we need to do
Render a circle to point the cannon at.

This is done by generating a distance above the target ship to render the circle at.

This is done by generating an angle of elevation and using the distance to the target ship to calculate the height above the target ship to render the circle at. (trigonometry)

We generate the angle of elevation by using the formula
![image](https://wikimedia.org/api/rest_v1/media/math/render/svg/4db61cb4c3140b763d9480e51f90050967288397)

This, so far, has relied on the ship being stationary, however the ship will move, so we need to predict where the ship will be in the future.

So we need to get the ship's future position, given time. 

Then we can aim at the ships future position for the x axis, but to get the y coordinate / distance above the ship we need to know the time it will take for the cannonball to reach the ship. But this time will change based on the angle of elevation. There is _apparently_ a [formula for this](https://www.forrestthewoods.com/blog/solving_ballistic_trajectories/), but it is a quartic equation and looks like lots of work. So instead I use an iterative approach.

I start with 0 seconds and aim at the current position of the ship
- we measure the time it takes for the cannonball to reach the ship
- we create a future position using this time
- we measure the distance between the future position and the target ship
- if the distance is larger than > 0.5 seconds we make a new prediction using the time we calculated, and continue to loop until the distance is small enough.

Now we have the tof to use we can get the future position and the angle of elevation to use.

## Getting the future position
The hardest part of this, by far is getting the future position of the ship. This is done by using the ships linear velocity, angular velocity and yaw to predict where the ship will be in the future.
- We initalise our variables first
```python
a_v = angular_velocity
l_v = linear_velocity
l_v_x = l_v[0]
l_v_y = l_v[1]

# We need to use the linear velocity of the local ship as well
l_v_local = local_linear_velocity
l_v_local_x = l_v_local[0]
l_v_local_y = l_v_local[1]

# I remove the local velocity to create a relative future position
l_v_x -= l_v_local_x
l_v_y -= l_v_local_y

# SoT uses degrees for yaw, so we convert it to radians
yaw = math.radians(yaw)
```
- We then calculate the magnitude of the linear velocity (sqrt(x^2 + y^2))
- If the magnitude is 0, we return the current position of the ship
- If the a_v is low (eg -0.3>a_v<0.3), return the position of the ship + the linear velocity * time (because the ship is travelling in a straight line)
- Get the radius (magnitude of the linear velocity / angular velocity)
- mogistink says that you should multiply the radius by 0.98 bc it just works (optional)
- Get the heading of the ship (atan2(l_v_y, l_v_x))
- If the ship is close to a boundary condition (when heading or yaw is near 0 or 2pi) we need to add or subtract 2pi to get the correct heading
- define wether the ship is turning left or right (left = a_v < 0)
- We need to get the angle to the center of rotation
```python
if left:
    theta = h - math.pi/2
else:
    theta = h + math.pi/2
```
- multiply radius by -1 if the ship is turning left
- get the center of rotation using trigonometry
```python
f_x = c_x + r * math.cos(theta + a_v * time)
f_y = c_y + r * math.sin(theta + a_v * time)
```
- return these coords as x and y, and the ship's current z coord as z (the ship doesnt move up and down or at least we dont calculate it)
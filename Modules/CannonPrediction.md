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
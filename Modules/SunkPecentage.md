# Sunk Percentage Module
This module is largely the same as the GenericActorDisplay module, with a few changes.

You can find the sunk percentage actor in your actor search, add these mappings to your mapping.py file:
```
waters = {
    "BP_SmallShip_StandardHull_InternalShipWater_C": {
        "name": "Small Ship Water",
    },
    "BP_MediumShip_StandardHull_InternalShipWater_C": {
        "name": "Medium Ship Water",
    },
    "BP_LargeShip_StandardHull_InternalShipWater_C": {
        "name": "Large Ship Water",
    },
}

water_keys = set(waters.keys())
```
Be sure to import these keys to your sot_hack.py file.

## New Things to Include
- import calculate_distance from helpers
- add the offsets for ShipInternalWater.WaterAmount, ShipInternalWater.InternalWaterParams and ShipInternalWaterParams.MaxWaterAmount to your offsets.json (these offsets are in my offsets.json)
- in the __init__ function
    - define the distance to the water actor (this is given by (calculate_distance(self.my_coords, self.coords))
    - get the water percentage by calling self._get_water_percentage (write this later)

- def the _get_water_percentage function
    - get the water amount by reading the float at (self.address + ShipInternalWater.WaterAmount)
    - get the max water amount by reading the float at (self.address + ShipInternalWater.InternalWaterParams + ShipInternalWaterParams.MaxWaterAmount)
    - return the water percentage (water amount / max water amount)
    - you should math.floor the percentage to get a whole number for display

- def the _build_text_string function
    - return a string that includes the water percentage or is solely the water percentage

- in update function
    - update the distance to the water actor (this is given by (calculate_distance(self.my_coords, self.coords))
    - update the water percentage by calling self._get_water_percentage (write this later)

    - if the distance is too far you may not want to display the percentage, you can do this by checking if the distance is greater than a certain value (e.g 1000) and if so, set self.to_delete to True, delete the text render and return

## Bonus Fun
If the water is nearby (e.g. within 15m of the player) I like to display the percentage on my screen at the bottom in the middle so that i can clearly see how close I am to sinking. To do this you can just check if the distance is close and then just manually set the x and y to be a specific spot on your screen.

## More Bonus Fun
I like to make the percentage increase in size the higher it gets, you could use a curve for this or just keep it simple and have the size increase according to the function font_size = 10 + self.water / 2

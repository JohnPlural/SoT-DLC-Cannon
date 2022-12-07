# Hole Number and Individual Hole display
Similarly to the water display, there is an actor for each ship's hull size, here are the mappings:
```python
hulls = {
    # ------------ HULLS ------------
    "BP_SmallShip_StandardHull_Damage_C": {
        "Name": "Small Ship Hull",
    },
    "BP_MediumShip_StandardHull_Damage_C": {
        "Name": "Medium Ship Hull",
    },
    "BP_LargeShip_StandardHull_Damage_C": {
        "Name": "Large Ship Hull",
    }
}

hull_keys = set(hulls.keys())
```
Be sure to import these keys to your sot_hack.py file.

## New Things to Include
You can largely do the same as water display, but there are a few differences:
- in the __init__ function
    - initialise an array of active hole addresses (self.active_holes = self._get_active_holes())
    - set self.holes to the length of the array (self.holes = len(self.active_holes))

- def the _get_active_holes function
    - read the [TArray](../TArray.md) at (self.address + HullDamage.ActiveHullDamageZones)
    - the number of holes is the second value in the TArray
    - starting at the starting address (the first value in the TArray) read the addresses of the holes by read_ptr, iterating by 8 bytes (the size of a pointer) and append each address to an array

- in update function
    - update the active holes array (self.active_holes = self._get_active_holes())
    - update the number of holes (self.holes = len(self.active_holes))

- in the sot_hack.py file, after finding the hull actor, iterate over the damage.holes (array of hole addresses) and create some [generic display objects](./GenericActorDisplay.md) for each hole.
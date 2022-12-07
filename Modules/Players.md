# Player Health and Name
Largely, the same as [GenericActorDisplay](./GenericActorDisplay.md), but with a few differences.

## New Things to Include
- in the __init__ function
    - initialise the player's health (self.health = self._get_health())
    - initialise the player's name (self.name = self._get_name())

- def the _get_health function
    - get the player health component by reading the ptr at (self.address + AthenaCharacter.HealthComponent)
    - get the health by reading the float at (health_component + HealthComponent.CurrentHealthInfo), round it and return

- def the _get_name function
    - get the playerstate by reading the ptr at (self.address + Pawn.PlayerState)
    - get the name location by reading the ptr at (playyerstate + PlayerState.PlayerName)
    - use read_name_string(name_location) and return the result

- change the _build_text_string function to include the player's name and health

- in the update function
    - you need to update the players health (not their name bc it doesn't change), and then ofc update the text string and render

## Fun Bonus
Healthbar is useful for gauging the enemies health without having to read a number. I implement this by adding a black rectangle underneath the player indicatior and then a green rectangle over the top that changes width depending on the health. You need to update this in the update function.

## More Fun Bonus
I like to make the healthbar change color depending on the health, my function for this is:
```python
# Colors the healthbar based on what weapon could one-hit you
# when health is 100% the color is green (blunderbuss can one-hit)
# when health is 70% the color is yellow (eye of reach can one-hit)
# when health is 55% the color is orange (flintlock can one-hit)
# when health is 25% the color is red (cutlass can one-hit)
color = (255, 0, 0)

if health > 70:
    color = (0, 255, 0)
elif health > 55:
    color = (255, 255, 0)
elif health > 25:
    color = (255, 165, 0)
```
This color needs to be updated in the update function and added to the render.
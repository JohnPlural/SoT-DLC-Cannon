# Generic Actor Display
This is the generic method for displaying any actor that you have an address for (assuming it has coordinates)

## Important Things to Include
- def the __init__ function, 
    - use the super().__init__(memory_reader) to get required functions

    - define the actor id, address, root_comp_ptr and my_coords (these should be required arguments)
    - get the actor's coordinates (using _coord_builder)
    - generate their screen coords (using object_to_screen)

    - initialise the text string by calling self._built_text_string (write this later)
    - initialise the text render by calling self._built_text_render (write this later)

    - initialise self.to_delete to False

- def the _build_text_string function
    - this should return a string that will be displayed on the screen
    - this should be called in the __init__ function

- def the _build_text_render function
    - this should return a text render object (pyglet Label)
    - The Label should include:
        - the text string
        - the x and y coordinates of the actor
        - main batch
        - the font size
        - the color
        - any other text elements you wish to add (e.g bold/italic, font, etc)
    - this should be called in the __init__ function

- def the update function
    - this should update the actor's coordinates
    - this should update the actor's screen coordinates
    - if necessary this should also update the text string and text render

    - you must check if the actor is still alive (using the actor id), this is done by comparing self._get_actor_id(self.address) with the stored actor id
        - if the id is different, set self.to_delete to True, delete the text render and return

    - check if the actor is still on screen (using the screen coordinates)
        - if the actor is not on screen, set self.to_delete to True, delete the text render and return, otherwise update the text render's x and y coordinates to the screen coords

# TArrays
TArrays are suprisingly simple. They are just an array with the format:

(starting address, length, max size)

The starting address is the address of the first element in the array. The length is the number of elements in the array. The max size im not entirely sure about, but it seems to be the maximum number of elements that can be in the array.

## Reading TArrays
You could be smart and add a function to memory_helper.py that reads TArrays, but I just read the TArray manually every time.
This is done by read_bytes, and then unpacking the raw bytes into a TArray struct.
```python
array_raw = read_bytes(address + offset, 0x10)
array = struct.unpack("<Qii", array_raw)

# TArray struct
starting_address = array[0]
length = array[1]
max_size = array[2]

# Iterating over the TArray (assuming its an array of pointers to addresses)
for i in range(length):
    object_address = read_ptr(starting_address + (i * 8))
```

## Uses
I use TArrays to find the masts on a ship, the active holes, the cannons on a ship. I have tried to use them to find the players on a ship, but I have not been able to get it to work, this was using a TArray of "Weakclass OnboardCharacters", which I couldnt figure out, hmu if you can get OnboardCharacters.
# Usage #

## Include library ##

```
#include <MaxMatrix.h>
```

## Create & Init MaxMatrix Object ##
```
MaxMatrix m(dataIn, load, clock, maxInUse);
m.Init();
```
  * `maxInUse` is the numbers of MAX7219 used.

## Set Intensity of the LED matrix ##
```
m.setIntensity(value)
```
  * `value` is the new intensity value, ranges from 0 to 15.

## Update a specified column ##
```
m.setColumn(col, value);
```
  * `col` is the index of column to be updated, range from 0 to 79.
  * `value` is the new values of LEDs, encoded as a unsigned byte.

## Update a specified LED ##
```
m.setDot(col, row, value)
```
  * `col` is the index of column of the LED to be updated, range from 0 to 79.
  * `row` is the index of row of the LED to be updated, range from 0 to 7.
  * `value` is the new values of LED, encoded as boolean value (HIGH / LOW).

## Update the same column of all LED matrix ##
```
m.setColumnAll(col, value);
```
  * `col` is the index of column to be updated, range from [- 7](0.md).
  * `value` is the new values of LEDs, encoded as a unsigned byte.

## Write a sprite ##
```
m.writeSprite(x, y, sprite)
```
  * `x` is the index of column of the sprite to be displayed, range from 0 to 79.
  * `y` is the index of row of the sprite to be displayed, range from 0 to 7.
  * `sprite` is the pointer to a byte array, encoding the contains of the sprite.

The format of the sprite is as follow:
  * sprite[0](0.md): the number of columns of the sprite, should be >= 1
  * sprite[1](1.md): the number of rows of the sprite, should be >= 1
  * sprite[- n](2.md): the sprite contents, encoded in column-based unsigned bytes, each array element represents 1 column of the sprite. If the sprite has n rows (n < 8), only the n LMS bits of each byte is used.

The code below display the sprite of letter "A" on the LED matrix
```
// CH_A stores the sprite 'A'
//
//  **
// *  *
// *  *
// *  *
// ****
// *  *
// *  *
byte CH_A[] = {4, 8, B1111110, B0010001, B0010001, B1111110 };
m.writeSprite(0, 0, sprite);
```


## Shift the matrix ##
```
m.shiftLeft(rotate = false, fill_zero = true);
m.shiftRight(rotate = false, fill_zero = true);
m.shiftUp(rotate = false, fill_zero = true);
m.shiftDown(rotate = false, fill_zero = true);
```
These 4 functions shift the values of matrix in one of the direction, by default the shift-in row/column is filled with zero (LED off). To rotate the LED values (i.e. the shift-in row/column is filled with shift-out values), you can set the rotate flag to true.
```
m.shiftLeft(true);
```
You can also shift in the value outside the matrix, by first uploading the values in the buffer (outside the matrix space you used) and calling the shiftLeft function. This is useful for creating scrolling message and animation. The code below shows how to scroll the letter 'A' into the LED matrix (assume one MAX7219 is used):
```
byte CH_A[] = {4, 8, B1111110, B0010001, B0010001, B1111110 };
m.writeSprite(8, 0, sprite); // write sprite OUTSIDE the LED matrix
for (int i=0; i<CH_A[0]; i++)
{
  m.shiftLeft(false, false);
  delay(100);
}
```
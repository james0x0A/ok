iKe
===
iKe is an experimental programming environment built on oK. It allows you to rapidly write event-driven graphical programs in K. Try it [In your browser](http://johnearnest.github.io/ok/ike/ike.html)!

The interface consists of an editor pane on the left with a status bar and display on the right. Pressing shift+enter in the editor will compile and run the K program. Control+C will halt a running program. Pressing shift+enter with a section of text selected will execute just that snippet and display the results in the status bar, allowing quick experimentation and sanity checking.

Output
------
To draw to the display, you must provide a definition of a niladic function named `draw`. This will be called 30 times per second and should return a list of tuples. Each tuple must contain a vector indicating an (x;y) position on screen, a palette and a bitmap. The palette consists of a list of strings representing any valid DOM color code such as "black" or "#FAC". The bitmap is a matrix of indices into the palette. By default, the display is 160x160 pixels.

For example, the following definition of `draw` will draw a small blue on red box at a random position:

	draw: {,(2?w;("#00F";"#F00");(1111b;1001b;1001b;1111b))}

Tuples will be drawn in the order they appear. For improved flexibility, any field of the tuple can be replaced with a symbol. Symbols will be looked up as variables when drawing. The above example could be rewritten as follows:

	p: 2?w
	c: ("#00F";"#F00")
	b: (1111b
	    1001b
	    1001b
	    1111b)
	draw: {,`p`c`b}

Observe how in the first example the box is drawn at a different position every time, but in the second example it is drawn in a consistent position as `2?w` is only evaluated once and then stored.

If the "position" element of any drawing tuple is a matrix instead of a vector, the bitmap will be drawn several times at each (x;y) pair:

	draw: {,((5 5;30 5;18 20);`lcd;text@6)}

This makes it very easy to draw many identical objects simulatenously.

If the "bitmap" element of any drawing tuple is a number or a vector, it will be drawn as a single pixel or a horizontal strip of pixels, respectively. Drawing single pixels in this way is generally quite inefficient, but drawing horizontal strips in combination with a vector of positions can produce some interesting "rasterbar" effects.

Input Events
------------
For dynamic behavior, iKe will call a number of K functions (provided they have been defined) whenever certain events occur:

- `tick`: called 30 times per second with no arguments.
- `kd`: key down. provides a DOM keyCode as an argument.
- `ku`: key up. provides a DOM keyCode as an argument.
- `lx`: left/right. provides -1/1 when left/right cursor keys are pressed.
- `ux`: up/down. provides -1/1 when up/down cursor keys are pressed.
- `md`: mouse down. provides mouse x and y in pixels as arguments.
- `mu`: mouse up. provides mouse x and y in pixels as arguments.
- `mm`: mouse moved. provides mouse x and y in pixels as arguments.

Variables
---------
iKe pre-defines and updates several K variables for your convenience:

- `w`: the width of the screen in pixels (read only)
- `h`: the height of the screen in pixels (read only)
- `f`: counts up once for each drawn frame (read only)
- `dir`: a 2d vector with the (x;y) offset of the cursor keys currently held (read only)
- `keys`: a vector of the keycodes currently held (read only)

iKe provides a number of pre-defined palettes. Since transparency is useful, the last color of each palette is fully transparent:

- `cga`: CGA palette 1 (4 colors + transparency)
- `hot`: hot dog stand (4 colors + transparency)
- `lcd`: Similar to the pea-soup LCD display of the GameBoy (4 colors + transparency)
- `solarized`: The [solarized](http://ethanschoonover.com/solarized) palette. (16 colors + transparency)
- `dawnbringer`: [dawnbringer's](http://pixeljoint.com/forum/forum_posts.asp?TID=12795) pixel art palette. (16 colors + transparency)
- `windows`: Windows 3.1 palette (16 colors + transparency)

![palettes](https://raw.githubusercontent.com/JohnEarnest/ok/gh-pages/ike/img/swatches.png)

iKe also provides a built-in 8x8 character set called `text`:

	t: ~,/'+text@`i$"Hello, World!"
	draw: {,(0 0;`cga;`t)}

The character set is aligned with 7-bit ASCII and control characters are replaced with some useful graphic characters including symbols and box drawing characters:

![font](https://raw.githubusercontent.com/JohnEarnest/ok/gh-pages/ike/img/font.png)

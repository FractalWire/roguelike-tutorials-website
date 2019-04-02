---
title: "Part 1 - Drawing the '@' symbol and moving it around"
date: 2019-03-30T08:39:15-07:00
draft: false
---

Welcome to part 1 of the **Roguelike Tutorial Revised**! This series
will help you create your very first roguelike game, written in Python!

This tutorial is largely based off the [one found on
Roguebasin](http://www.roguebasin.com/index.php?title=Complete_Roguelike_Tutorial,_using_python%2Blibtcod).
Many of the design decisions were many to keep this tutorial in lockstep
with that one (at least in terms of chapter composition and general
direction). This tutorial would not have been possible without the
guidance of those who wrote that tutorial, along with all the wonderful
contributors to libtcod and python-tcod over the years.

This part assumes that you have either checked [Part
0](/tutorials/tcod/part-0) and are already set up and ready to go. If
not, be sure to check that page, and make sure that you've got Python
and TCOD installed, and a file called `engine.py` created in the
directory that you want to work in.

Assuming that you've done all that, let's get started. Modify (or
create, if you haven't already) the file `engine.py` to look like this:

```py3
import tcod as libtcod


def main():
    print('Hello World!')


if __name__ == '__main__':
    main()
```

You can run the program by like any other Python program, but for those
who are brand new, you do that by typing `python engine.py` in the
terminal. If you have both Python 2 and 3 installed on your machine, you
might have to use `python3 engine.py` to run (it depends on your default
python, and whether you're using a virtualenv or not).

Okay, not the most exciting program in the world, I admit, but we've
already got our first major difference from the other tutorial. Namely,
this funky looking thing here:

```py3
if __name__ == '__main__':
    main()
```

So what does that do? Basically, we're saying that we're only going to
run the "main" function when we explicitly run the script, using
`python engine.py`. It's not super important that you understand this
now, but if you want a more detailed explanation, [this answer on Stack
Overflow](https://stackoverflow.com/a/419185) gives a pretty good
overview.

Confirm that the above program runs (if not, there's probably an issue
with your libtcod setup). Once that's done, we can move on to bigger and
better things. The first major step to creating any roguelike is getting
an '@' character on the screen and moving, so let's get started with
that.

Modify `engine.py` to look like this:

```py3
import tcod as libtcod


def main():
    screen_width = 80
    screen_height = 50

    libtcod.console_set_custom_font('arial10x10.png', libtcod.FONT_TYPE_GREYSCALE | libtcod.FONT_LAYOUT_TCOD)

    libtcod.console_init_root(screen_width, screen_height, 'libtcod tutorial revised', False)

    while not libtcod.console_is_window_closed():
        libtcod.console_set_default_foreground(0, libtcod.white)
        libtcod.console_put_char(0, 1, 1, '@', libtcod.BKGND_NONE)
        libtcod.console_flush()

        key = libtcod.console_check_for_keypress()

        if key.vk == libtcod.KEY_ESCAPE:
            return True


if __name__ == '__main__':
    main()
```

Run `engine.py` again, and you should see an '@' symbol on the screen.
Once you've fully soaked in the glory on the screen in front of you, you
can hit the \`Esc\` key to exit the program.

There's a lot going on here, so let's break it down line by line.

```py3
    screen_width = 80
    screen_height = 50
```

This is simple enough. We're defining some variables for the screen
size. Eventually, we'll load these values from a JSON file rather than
hard coding them in the source, but we won't worry about that until we
have some more variables like this.

```py3
    libtcod.console_set_custom_font('arial10x10.png', libtcod.FONT_TYPE_GREYSCALE | libtcod.FONT_LAYOUT_TCOD)
```

Here, we're telling libtcod which font to use. The `'arial10x10.png'`
bit is the actual file we're reading from (this should exist in your
project folder). The other two parts are telling libtcod which type of
file we're reading.

```py3
    libtcod.console_init_root(SCREEN_WIDTH, SCREEN_HEIGHT, 'libtcod tutorial revised', False)
```

This line is what actually creates the screen. We're giving it the
`screen_width` and `screen_height` values from before (80 and 50,
respectively), along with a title (change this if you've already got
your game's name figured out), and a boolean value that tells libtcod
whether to go full screen or not.

```py3
    while not libtcod.console_is_window_closed():
```

This is what's called our 'game loop'. Basically, this is a loop that
won't ever end, until we close the screen. Every game has some sort of
game loop or another.

```py3
        libtcod.console_set_default_foreground(0, libtcod.white)
```

This line tells libtcod to set the color for our '@' symbol. If you want
your character to be a different color, change `libtcod.white` to
something like `libtcod.red` and see what happens. The '0' in this
function is the console we're drawing to. We'll go over that more later.

```py3
        libtcod.console_put_char(0, 1, 1, '@', libtcod.BKGND_NONE)
```

The first argument is '0' (again, the console we're printing to). The
next two are x and y coordinates, in this case, 1 and 1 (try changing
that and see what happens). Next, we're printing the '@' symbol, and
setting the background to 'none' with `libtcod.BKGND_NONE`.

```py3
        libtcod.console_flush()
```

This is the part that presents everything on the screen. Pretty
straightforward.

```py3
        key = libtcod.console_check_for_keypress()

        if key.vk == libtcod.KEY_ESCAPE:
            return True
```

This part gives us a way to gracefully exit (i.e. not crashing) the
program by hitting the `Esc` key. The
`libtcod.console_check_for_keypress()` function gets any keyboard input
to the program, which we store in the `key` variable. From there, we
check if the key pressed was the `Esc` key or not. If it was, then we
exit the loop, thus ending the program.

So we've got our '@' symbol drawn, now let's get it moving around!

We need to keep track of the player's position at all times, so let's
create two variables, `player_x` and `player_y` to keep track of this.

```diff
    ....
    screen_height = 50
+     
+     player_x = int(screen_width / 2)
+     player_y = int(screen_height / 2)
+     
    libtcod.console_set_custom_font('arial10x10.png', libtcod.FONT_TYPE_GREYSCALE | libtcod.FONT_LAYOUT_TCOD)
    ...
```

*Note: Ellipses denote omitted parts of the code. I'll include lines
around the code to be inserted so that you'll know exactly where to put
new pieces of code, but I won't be showing the entire file every time.
The green lines denote code that you should be adding.*

We're placing the player right in the middle of the screen. What's with
the `int()` function though? Well, Python 3 doesn't automatically
truncate division like Python 2 does, so we have to cast the division
result (a float) to an integer. If we don't, libtcod will give an error.

We also have to modify the command to put the '@' symbol to use these
new coordinates.

```diff
        ...
        libtcod.console_set_default_foreground(0, libtcod.white)
-         libtcod.console_put_char(0, 1, 1, '@', libtcod.BKGND_NONE)
+         libtcod.console_put_char(0, player_x, player_y, '@', libtcod.BKGND_NONE)
        libtcod.console_flush()
        ...
```

*Note: The red lines denote code that has been removed.*

Run the code now and you should see the '@' in the center of the screen.
Let's take care of moving it around now.

Put the following two lines right above the main game loop.

```diff
    ...
    libtcod.console_init_root(screen_width, screen_height, 'libtcod tutorial revised', False)

+     key = libtcod.Key()
+     mouse = libtcod.Mouse()

    while not libtcod.console_is_window_closed():
    ...
```

As the names imply, these variables will hold our keyboard and mouse
input. We aren't implementing the mouse yet, but the function we're
about to add take it into account, so we might as well add it.

```diff
    ...
    while not libtcod.console_is_window_closed():
+         libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS, key, mouse)

        libtcod.console_set_default_foreground(0, libtcod.white)
    ...
```

This is the function that actually captures new "events" (user input).
It will update the `key` and `mouse` variables with what the user
inputs. Again, we're only concerned with `key` for right now.

Okay, so we're updating the `key` variable with the user's input. But
what do we actually *do* with it? Let's define a function to handle the
user's input. It will essentially translate the user's key presses into
game actions.

Up until now, this tutorial hasn't deviated all that much from the
original one, but here's a critical turning point. We're about to define
a function, called `handle_keys` to take care of keyboard input. We
*could* put this in our `engine.py` file... but should it be there? I
would argue no. The engine (game loop) captures input and should do
something with it, but translating from one to the other is not
something it needs to know about.

So rather than putting the `handle_keys` function in `engine.py`, let's
create a new file, called `input_handlers.py`. Put the following code
inside that new file.

```py3
import tcod as libtcod


def handle_keys(key):
    # Movement keys
    if key.vk == libtcod.KEY_UP:
        return {'move': (0, -1)}
    elif key.vk == libtcod.KEY_DOWN:
        return {'move': (0, 1)}
    elif key.vk == libtcod.KEY_LEFT:
        return {'move': (-1, 0)}
    elif key.vk == libtcod.KEY_RIGHT:
        return {'move': (1, 0)}

    if key.vk == libtcod.KEY_ENTER and key.lalt:
        # Alt+Enter: toggle full screen
        return {'fullscreen': True}

    elif key.vk == libtcod.KEY_ESCAPE:
        # Exit the game
        return {'exit': True}

    # No key was pressed
    return {}
```

That's a lot to take in all at once, so again, let's break it down a
bit.

```py3
def handle_keys(key):
```

We're defining a function called `handle_keys`, which takes one
argument, `key`. `key` in this case will be the key variable we captured
earlier.

```py3
    if key.vk == libtcod.KEY_UP:
```

This if statement (along with the other elifs) just tell us which key
was pressed. Right now, it's one of the arrow keys for movement. What's
more interesting is the code inside these if statements

```py3
    return {'move': (0, -1)}
```

So what's going on here? Well, when we return from this function, the
engine is going to have to do something. In this case, we want our
character to move. But what if we hit a different key? Then we might not
be moving; we may be using an item, casting a spell, or exiting the
game. One way to handle all these different possibilities is to return a
dictionary from this function, which the engine will read and decide
what to do.

In this instance, we're returning a dictionary with the key `'move'`,
and the value is a pair of numbers. The numbers will tell the engine in
what direction to move the player. So for example, the 'up' key will
move us '0' on the x axis, and '-1' on the y axis.

```py3
    if key.vk == libtcod.KEY_ENTER and key.lalt:
        # Alt+Enter: toggle full screen
        return {'fullscreen': True}
    elif key.vk == libtcod.KEY_ESCAPE:
        # Exit the game
        return {'exit': True}
```

These are our non-movement actions that we're allowing for now. If the
user presset ALT+Enter, the game will go full screen. If the user
presses 'Esc', the game will exit.

```py3
    return {}
```

Because our engine will be expecting a dictionary, we have to return
*something*, even if nothing happened.

This may seem confusing, but it will likely make sense in a minute.
Let's return to our `engine.py` file and call our `handle_keys`
function.

```diff
        ...
        libtcod.console_flush()

-         key = libtcod.console_check_for_keypress()
+         action = handle_keys(key)
+ 
+         move = action.get('move')
+         exit = action.get('exit')
+         fullscreen = action.get('fullscreen')

+         if move:
+             dx, dy = move
+             player_x += dx
+             player_y += dy

-         if key.vk == libtcod.KEY_ESCAPE:
+         if exit:
+             return True
+ 
+         if fullscreen:
+             libtcod.console_set_fullscreen(not libtcod.console_is_fullscreen())
        ...
```

Note: I'll denote lines to delete in red. So in this case, remove the
`key = libtcod.console_check_for_keypress()` and
`if key.vk == libtcod.KEY_ESCAPE` lines.

Also be sure to import the `handle_keys` function at the top of
`engine.py`.

```diff
import tcod as libtcod

+ from input_handlers import handle_keys
```

Hopefully now the dictionary madness in `handle_keys` makes a little
more sense. We're capturing the return value of `handle_keys` in the
variable `action` (which should be a dictionary, no matter what we
pressed), and checking what keys are inside it. If it contains a key
called 'move', then we know to look for the (x, y) coordinates. If it
contains 'exit', then we know we need to exit the game.

Try running the engine.py file now. You should be able to move around.
Exciting!

One last thing before we move on. Take a look at our drawing functions.
Notice how the first argument is '0'? In truth, that represents the
current 'console' we are drawing to, 0 is the default. Rather than just
drawing to the default we'll want to specify which console to draw to,
after initiating a new one. The reasoning is that it will make it easier
to make new consoles and draw to them in the future. This will be
especially useful when we get to the GUI portion of this series.

Modify the `engine.py` file like this:

```diff
    ...
    libtcod.console_init_root(screen_width, screen_height, 'libtcod tutorial revised', False)

+     con = libtcod.console_new(screen_width, screen_height)

    key = libtcod.Key()
    mouse = libtcod.Mouse()

    while not libtcod.console_is_window_closed():
        libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS, key, mouse)
+         
+         libtcod.console_set_default_foreground(con, libtcod.white)
+         libtcod.console_put_char(con, player_x, player_y, '@', libtcod.BKGND_NONE)
+         libtcod.console_blit(con, 0, 0, screen_width, screen_height, 0, 0, 0)
-         libtcod.console_set_default_foreground(0, libtcod.white)
-         libtcod.console_put_char(0, player_x, player_y, '@', libtcod.BKGND_NONE)
        libtcod.console_flush()
+         
+         libtcod.console_put_char(con, player_x, player_y, ' ', libtcod.BKGND_NONE)
-         libtcod.console_put_char(0, player_x, player_y, ' ', libtcod.BKGND_NONE)
```

That wraps up part one of this tutorial! If you're using git or some
other form of version control (and I recommend you do), commit your
changes now.

If you want to see the code so far in its entirety, [click
here](https://github.com/TStand90/roguelike_tutorial_revised/tree/part1).
The files you'll want to check are `engine.py` and `input_handlers.py`

[Click here to move on to the next part of this
tutorial.](/tutorials/tcod/part-2)

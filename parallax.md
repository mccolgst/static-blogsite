Title: Parallax Scrolling
Date: 2017-05-09 20:20
Category: gamedev
Tags: gamedev, pico8
Slug: parallax

I participated in the Ludum Dare 38 challenge. You can see [my game entry here](https://ldjam.com/events/ludum-dare/38/momma-zilla)

One of the many great pieces of feedback I received was that there isn't much going on in the way of a background.

In many other games, I've seen cool scrolling background effects, including the parallax background scrolling effect. 

What is the parallax effect? Simply put, it's cool trick to seemingly give depth to a game by making the background scroll at varying speeds. Things moving slower will appear further away, things moving faster will appear closer. You can read more about it [here on wikipedia](https://en.wikipedia.org/wiki/Parallax)

You can see the effects of this scrolling background here:

![momma zilla parallax scroll]({filename}/images/parallax.gif)

But how does it work?

Fortunately, it's pretty simple! I'll show you how I did it with [pico-8](https://www.lexaloffle.com/pico-8.php)

First, you need a background image that can scroll:

![scroll step 1]({filename}/images/scroll_1.gif)

This part is easy, move the x of the sprite over, once the xposition of the sprite minus the width of the sprite is less than 0 (or to say that the rightmost part of the sprite has gone offscreen to the left), then put it back into view:

```
function update_plx_scrl(plx_scrl)
  plx_scrl.x+=plx_scrl.spd
  if plx_scrl.x<=-(plx_scrl.w*8) then
    plx_scrl.x=plx_scrl.w*8
  end
end
```

There's a bit of trouble though. It seems like for most of the time, we don't actually have all of our background on the screen!

One way we can fix this is by actually having TWO copies of the images lined up together, and once one of them goes offscreen, put it back ahead of the one that is currently on screen! Here you can see what I mean:

![scroll step 2]({filename}/images/scroll_2.gif)

I've put a red line so you can see where one image ends and the next one begins.

Now all we have to do is create several images that simply move at different speeds:

![scroll step 3]({filename}/images/scroll_3.gif)


Pretty cool, right!


Here's a quick before and after - I think it really helps with the mood and depth of the game:

![before]({filename}/images/before.gif)
![after]({filename}/images/parallax.gif)



Here's the final code:
```
plx_scrls = {}


function new_plx_scrl(h, w, x, y, spd, sp)
  local plx_scrl = {}
  plx_scrl.w = w
  plx_scrl.h = h
  plx_scrl.x = x
  plx_scrl.y = y
  plx_scrl.sp = sp
  plx_scrl.spd = spd
  
  // second one is for smooth cycle
  local plx_scrl2 = {}
  plx_scrl2.w = w
  plx_scrl2.h = h
  plx_scrl2.x = x+(w*8)
  plx_scrl2.y = y
  plx_scrl2.sp = sp
  plx_scrl2.spd = spd
  
  add(plx_scrls, plx_scrl)
  add(plx_scrls, plx_scrl2)
end

function update_plx_scrl(plx_scrl)
  plx_scrl.x+=plx_scrl.spd
  if plx_scrl.x<=-(plx_scrl.w*8) then
    plx_scrl.x=plx_scrl.w*8
  end
end

function draw_plx_scrl(plx_scrl)
  spr(plx_scrl.sp,
      plx_scrl.x,
      plx_scrl.y,
      plx_scrl.w,
      plx_scrl.h)
end

function _init()
  new_plx_scrl(3,16,0,20,-.25,128)
  new_plx_scrl(3,16,0,25,-.5,64)
  new_plx_scrl(3,16,0,30,-1,0)
  
end

function _update()
  foreach(plx_scrls, update_plx_scrl)
end

function _draw()
  cls()
  rectfill(0,0,128,128,5)
  foreach(plx_scrls, draw_plx_scrl)
end
```

If you have a copy of PICO-8, you can play around with the cart here:

![parallax cart]({filename}/images/plx.p8.png)

or check out the github project where I keep my carts:
[https://github.com/mccolgst/pico-8-cartridges/blob/master/plx.p8](https://github.com/mccolgst/pico-8-cartridges/blob/master/plx.p8)

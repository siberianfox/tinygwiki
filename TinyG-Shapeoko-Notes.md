###Intro
Shopeoko and TinyG are a great fit. The combination make a good upgrade to support very smooth, fast motor operation, built-in support for dual Y axis configurations, and other enhancements. 

###What Do I get?
Motion control is very smooth due to precise timing and constant jerk acceleration. This means a number of things to the Shapeoko user. TinyG has an optimized, low jitter step generation coupled with constant jerk acceleration management. As a result TinyG gets a lot out of your motors. If you think you need to upgrade from the stock NEMA17 motors to something larger you may find that more precise control offered by TinyG is really all you need.

The constant jerk acceleration management also makes for extremely fast rapids (traverses), which helps cut down job times. It does all this with minimal shaking of the machine and toolhead, making for smoother cuts with less change of skipping, chattering, or other artifacts.

<Insert video here>

###Tgfx
TinyG works with tgfx, a cross-platform control program available for Mac, Windows and Linux.

###Setup

Setting up Shapeoko to use TinyG is pretty straightforward. This page provides all the settings needed
https://github.com/synthetos/TinyG/wiki/TinyG-Shapeoko-Setup

A few things to keep in mind.

* TinyG treats motors independently from the axes. So it natively supports dual-y configurations. 2 motors map to the Y axis - and both are driven by Y axis controls. But they must be going in opposite directions (i.e. have reverse polarity settings) for the gantry to move as a unit. Polarity can be handled electrically by reversing one of the coil pairs on one of the motors, or under software control by setting the polarity motor parameter. I prefer firmware as this way all the motors are wired the same and are interchangeable.

###Tuning
Here are some points to get the most out of the system.

* The velocity maximum settings determine how fast traverses (G0's) will move. We usually set these to 16000 mm/min (267 mm/sec for 3dp types), but these can often be set higher

* The mechanical system is the heart of tuning. The electrical system can at best compensate for it, but can never improvie it.

I think the blog post should start with 
A brief overview of the general advantages of the TinyG.
A bit on how that can help a Shapeoko user.
Setup instructions for a Shapeoko user
Tuning tips.
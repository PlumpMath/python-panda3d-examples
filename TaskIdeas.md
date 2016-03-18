# Task Ideas #


The results of each task will consist of:
  1. a program that implements the  task (optionally with supporting files that contain data or settings)
  1. a wiki page on this site annotating your program and explaining the major sections of the code.

# Proposed Tasks #

## 1. Create a more user friendly camera control system ##

The default camera control system is somewhat unintuitive as it does not match the typical camera control used in most games. Create and describe a camera control system with the following features:

  * Holding the right mouse button rotates the view
  * Scrolling with the scrolling wheel of the mouse button zooms in and out
  * Pressing the SPACE bar resets the view (optionally the reset could happen smoothly, with the camera floating back to the original view)

Recommendations: load any default model (panda or Ralph), a polar coordinate system might work best, use intervals to float the camera, see the "Roaming Ralph" sample source code for camera control examples.

## 2. Create a visible coordinate system for Panda3D ##

Initially one of the most confusing aspects of 3D programming consist of not being able to tell the orientation and location of objects in the 3D space. Create a visible coordinate system that shows all three axes and displays labels on them.

Recommendations: you may need to create a simple 3D model (a cylinder) that will be used (scaled and rotated) as an axis. You may use [MilkShape](http://chumbalum.swissquake.ch/) to easily create such a model. Use text nodes to display the labels. Tip: the coordinate system in Panda3D is a right handed z-up coordinate system -> X is to the right, Y is into the screen, and Z is up

## 3. Find interesting Particle Effects ##

Panda3D comes with highly customizable particle effects but there is little to no information on these effects look like or what parameters one might choose to simulate various desired outcomes: explosion, fire, splash, smoke, wapor, "cool magic effect" etc.

For this task you'll need to investigate and create five (or more) interesting effects.

Your program should load the parameters for each effect from an external file (.ini or json format) and display them in multiple ways: when the particle source is static, particle source moving on a linear and circular paths. Wiki page needs to show screenshots of the effects.

Recommendations: [Panda3D manual](http://www.panda3d.org/wiki/index.php/Particle_Effects) page on particle effects, look at the particle panel distributed as a Panda3D sample program (though it is slightly quirky on my machine)

## 4. Implement floating messages ##

For this task you will need to implement a common display mechanism, one that temporarily displays a slowly floating message. Most often the messages float upwards, change color as they move and/or fade out.

Create a program that loads up a model, then upon a keypress creates a message and floats it upwards combining multiple effects (color, transparency etc).

Recommendations: Use a text node and animate it via [Intervals](http://www.panda3d.org/wiki/index.php/Intervals). Combine multiple effects with Parallels or Sequences.
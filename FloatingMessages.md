# Introduction #

This program explains how to use Intervals, Parallels and Sequences to apply multiple effects to a TextNode.

Link to the source code http://python-panda3d-examples.googlecode.com/svn/trunk/floating-messages/pandafloatingmessages.py


# Code Explanation #

Import the required modules.

```
import sys, random
import direct.directbase.DirectStart
from pandac.PandaModules import *
from direct.interval.IntervalGlobal import *
from direct.gui.DirectGui import *
```

Called by DirectEntry when focused, used to clear the DirectEntry's text.

```
def clearText():
    directEntry.enterText("") # Clear the DirectEntry text
```

Function called by DirectEntry when Enter is pressed, used to display the user input.

```
def displayText(text):
    directEntry.enterText("") # Clear the DirectEntry text
    newTextNode = TextNode('text') # Create a new TextNode
    newTextNode.setText(text) # Set the TextNode text
    newTextNode.setAlign(TextNode.ACenter) # Set the text align
    newTextNode.setWordwrap(16.0) # Set the word wrap
    text_generate = newTextNode.generate() # Generate a NodePath
    newTextNodePath = render.attachNewNode(text_generate) # Attach the NodePath to the render tree
    newTextNodePath.setPos(0,20,0) # Set the NodePath position
    textEffects(newTextNodePath) # Call the function that displays the text effects
```


Display some text effects through Intervals, Sequence and Parallel.
Note that the '6' passed to each Interval is its duration expressed in seconds.

```
def textEffects(textNodePath):
    # New text position
    x = random.randint(-5,5)
    y = random.randint(10,40)
    z = random.randint(-5,5)
    tnpPosInterval = LerpPosInterval(textNodePath, 6, Point3(x,y,z))

    # The new text color in RGBA
    r = random.random()
    g = random.random()
    b = random.random()
    a = random.random()
    tnpColorInterval = LerpColorInterval(textNodePath, 6, VBase4(r,g,b,a))

    # Scale the text
    tnpScaleInterval=LerpScaleInterval(textNodePath, 6, 1+random.random()) 

    # Rotate the text
    current_rotation = textNodePath.getHpr()[2] # Get the current angle
    rotation=current_rotation+random.randint(60,120) # The new angle incremented by 60-120
    tnpHprInterval = LerpHprInterval(textNodePath, 6, Vec3(0,0,rotation))

    # Create the new Sequence and start it.
    # Notice that this function will be re-called at the end of the Sequence.
    # In this way we create a loop.
    textSequence = Sequence(Parallel(tnpPosInterval,tnpColorInterval,tnpScaleInterval,tnpHprInterval),Func(textEffects, textNodePath))
    textSequence.start()
```

Load, reparent, scale and position the environment model provided with Panda3D.

```
environ = loader.loadModel("models/environment") # Load the model
environ.reparentTo(render) # Reparent to the render tree
environ.setScale(0.2,0.2,0.2) # Scale the model
environ.setPos(-8,50,-4) # Set the model position
```

Create and position the DirectEntry used to get a string from the user.

```
directEntry = DirectEntry(text = "", scale=0.1, width=13, command=displayText,
                          initialText="Type some text then press Enter :)", numLines = 1,
                          focusInCommand=clearText)
directEntry.setPos(Point3(-0.7,0,-0.95))
```

Panda3D main loop.

```
run()
```

A screenshot.

![http://python-panda3d-examples.googlecode.com/svn/wiki/panda3dfloatingmessagesry0.png](http://python-panda3d-examples.googlecode.com/svn/wiki/panda3dfloatingmessagesry0.png)
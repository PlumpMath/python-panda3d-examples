# Introduction #
Panda's coordinate system in 2D and 3D may be difficult for some to grasp, as they both use different scales, and may be hard to visually depict in one's mind. The purpose of this tutorial is to show how the system appears, and most importantly, how it looks in code.

Link to the source code http://python-panda3d-examples.googlecode.com/svn/trunk/visible-coordinates/PandaCoords.py

# Analysis #
Now, let us dissect the code:

1. Load the required libraries
```
import direct.directbase.DirectStart
from direct.gui.OnscreenText import OnscreenText
from pandac.PandaModules import PandaNode,NodePath,Camera,TextNode
from direct.gui.DirectGui import *
```

2. First, we create a function that'll simplify the task of printing text. There are many ways to render text in Panda3d. Most methods are derived from the base class of TextNode. Thus, TextNode has more features than most other methods, and we'll use it here for that exact purpose.

```
def printText(name, message, color): 
    text = TextNode(name) # create a TextNode. Note that 'name' is not the text, rather it is a name that identifies the object.
    text.setText(message) # Here we set the text of the TextNode
    x,y,z = color # break apart the color tuple
    text.setTextColor(x,y,z, 1) # Set the text color from the color tuple

    text3d = NodePath(text) # Here we create a NodePath from the TextNode, so that we can manipulate it 'in world'
    text3d.reparentTo(render) # This is important. We reparent the text NodePath to the 'render' tree. 
# The render tree contains what is going to be rendered into the 3D WORLD. 
# If we reparented it to 'render2d' instead, it would appear in the 2D WORLD. 
# The 2D WORLD, from the documentation, is as if someone had written on your computer screen.
    return text3d # return the NodePath for further use
```

3. Now we'll draw fifty-one tick marks in every direction, x, y, z in the 3d world.
```
for i in range(0,51):
    printText("X", "|", (1,0,0)).setPos(i,0,0)  # note that "X" is the name of the TextNode, not its content. 
#Also note that we are going up in increments of 1.

for i in range(0,51):
    printText("Y", "|", (0,1,0)).setPos(0,i,0)  
        
for i in range(0,51):
    printText("Z", "-", (0,0,1)).setPos(0,0,i) 
```

4. Label the Axes. Notice how I set the position of the returned NodePath, adding 1 or .5  so that the Text is not covered by the ticks.
```
printText("XL", "X", (0,0,0)).setPos(11.5,0,0) 
printText("YL", "Y", (0,0,0)).setPos(1,10,0) 
printText("YL", "Z", (0,0,0)).setPos(1,0,10) 
printText("OL", "@", (0,0,0)).setPos(0,0,0) 
```

5. Now we set up the 2D Coordinates.
```
# We now use the OnscreenText function, which is derived from TextNode.
# It is restricted to 2D.

# Also, note how I divide everything by 10. 
# The 2D coordinate system goes from -1 to 1
# I did -2 to 2 just for kicks

OnscreenText(text = '(0 , 0)', pos = (.1, .05), scale = 0, fg=(1,1,1,1))
for i in range(-20,20):
    OnscreenText(text = '.', pos = (0, i/float(10)), scale = 0, fg=(1,1,1,1))
for i in range(-20,20):
    OnscreenText(text = '.', pos = (i/float(10), 0), scale = 0, fg=(1,1,1,1))
    
OnscreenText(text = 'X', pos = (1.3, .1), scale = 0, fg=(1,1,1,1))
OnscreenText(text = 'Y', pos = (.1, .9), scale = 0, fg=(1,1,1,1))


```

6. Add labels and other text: look at how the text are placed, compared with this code
```

OnscreenText(text="Panda 2D/3D: Coordinate System", style=1,  fg=(1,1,1,1), pos=(0.8,-0.95), scale = .07)

OnscreenText(text="Notes:", style=1,  fg=(1,1,1,1), pos=(0.3,0.8), scale = .07)
OnscreenText(text="- Each dot represents 0.10 units", style=1,  fg=(1,1,1,1), pos=(0.64,0.7), scale = .07)
OnscreenText(text="- Each dash represents 1 unit", style=1,  fg=(1,1,1,1), pos=(0.6,0.6), scale = .07)
      
```

7. Set up camera
```

# we must disable the mouse, or we can't control the camera from code
base.disableMouse() 

# set the camera 10 units left, -50 units back, and 10 units up so we can see the 3D coordinate system
base.camera.setPos(10,-50,10)
  

```

8. Run
```
run()
```

A screenshot.

![http://python-panda3d-examples.googlecode.com/svn/wiki/panda-coordinates.png](http://python-panda3d-examples.googlecode.com/svn/wiki/panda-coordinates.png)
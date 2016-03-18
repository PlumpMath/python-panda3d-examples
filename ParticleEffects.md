# Introduction #

This program explains how to use Particle Effects.

Link to the source code <>

# Code Explanation #

Import the required modules.
```
import sys, ConfigParser
import direct.directbase.DirectStart
from pandac.PandaModules import BaseParticleEmitter,BaseParticleRenderer,PointParticleRenderer
from pandac.PandaModules import PointParticleFactory,SpriteParticleRenderer
from pandac.PandaModules import DiscEmitter,TextNode
from pandac.PandaModules import Point3,Vec3,Vec4,Mat4
from direct.particles.Particles import Particles
from direct.particles.ParticleEffect import ParticleEffect
from direct.particles.ForceGroup import ForceGroup
from pandac.PandaModules import LinearNoiseForce,LinearVectorForce
from direct.gui.OnscreenText import OnscreenText
from direct.showbase.DirectObject import DirectObject
from direct.interval.MetaInterval import Sequence
from direct.interval.FunctionInterval import Func 
from direct.interval.IntervalGlobal import *
from direct.directutil import Mopath
from direct.interval.MopathInterval import *
```

Initialize the World class.
```
class World(DirectObject):
    def __init__(self):
        # Load the configuration file
        load_config('fire.ini')

        # Disable the mouse and set the camera's position
        base.disableMouse()
        camera.setPos(0, -40, 0)

        # Set the background color to black
        base.setBackgroundColor(0, 0, 0)

        # Load and setup the plant model
        self.plant = loader.loadModel("plants1")
        self.plant.reparentTo(render)
        self.plant.setScale(0.12,0.12,0.12)
        self.plant.setPos(0,0,0.2)

        # This command is required for Panda to render particles
        base.enableParticles()

        # Draw text on the screen
        OnscreenText(text = 'Particles Motion\ns : static\nl : linear\nc : circular',
                     fg = (1,1,1,1), pos = (-1.2, 0.8), scale = 0.07, align=TextNode.ALeft)
                     fg = (1,1,1,1), pos = (-1.2, 0.8), scale = 0.07, align=TextNode.ALeft)
        OnscreenText(text = 'Mouse Control\ne : enable\nd : disable',
                     fg = (1,1,1,1), pos = (0.6, 0.8), scale = 0.07, align=TextNode.ALeft)

        # Bind key events
        self.accept( 's' , self.static )
        self.accept( 'l' , self.linear )
        self.accept( 'c' , self.circular )
        self.accept( 'e', self.enable_mouse)
        self.accept( 'd', self.disable_mouse)

        self.sequence = None

        # Load and start the particles effect
        self.particle_effect = self.load_effect('fire-effect')
        self.particle_effect.start(render)
```

Mouse control functions.
```
    # Enable the mouse control and reset the camera position
    def enable_mouse(self):
        mat=Mat4(camera.getMat())
        mat.invertInPlace()
        base.mouseInterfaceNode.setMat(mat)
        base.enableMouse()

    # Disable the mouse control and reset the camera position
    def disable_mouse(self):
        base.disableMouse()
        camera.setPos(0, -40, 0)
        camera.setHpr(0, 0, 0)
```

Particle Effects motion functions.
```
    # Do the ParticleEffect static
    def static(self):
    	# Stop the sequence if not None
        if self.sequence:
            self.sequence.finish()
        # Reset the particle effect and the plant position
        self.particle_effect.setPos(0, 0, 0)
        self.plant.setPos(0,0,0.2)

    # Do the ParticleEffect's motion linear
    def linear(self):
    	# Stop the Sequence if not None
        if self.sequence:
            self.sequence.finish()
        # Set the particle effect and plant position
        self.particle_effect.setPos(-10, 0, 0)
        self.plant.setPos(-10, 0, 0)
        # Create the position intervals
        fire_interval1 = LerpPosInterval(self.particle_effect, 4, Point3(10, 0, 0))
        fire_interval2 = LerpPosInterval(self.particle_effect, 4, Point3(-10, 0, 0))
        plant_interval1 = LerpPosInterval(self.plant, 4, Point3(10, 0, 0))
        plant_interval2 = LerpPosInterval(self.plant, 4, Point3(-10, 0, 0))
        # Create the new Sequence and start it
        self.sequence = Sequence(Parallel(fire_interval1, plant_interval1), Parallel(plant_interval2, fire_interval2))
        self.sequence.loop()

    # Do the ParticleEffect's motion circular
    def circular(self):
    	# Stop the Sequence if not None
        if self.sequence:
            self.sequence.finish()
        # Create a path and load the circular model
        path = Mopath.Mopath()
        path.loadFile('circular.egg')
        # Create the path intervals
        fire_interval1 = MopathInterval(path,self.particle_effect, name='fire-circular-path', duration=8)
        plant_interval1 = MopathInterval(path,self.plant, name='plant-circular-path', duration=8)
        # Create the new Sequence and start it
        self.sequence = Sequence(Parallel(fire_interval1, plant_interval1))
        self.sequence.loop()
```

Create the particle effects and configure it.
```
    def load_effect(self, name):
        pe = ParticleEffect()
        pe.reset()
        pe.setPos(get_float(name, 'pos_x'),
                  get_float(name, 'pos_y'),
                  get_float(name, 'pos_z'))
        pe.setHpr(get_float(name, 'hpr_h'),
                  get_float(name, 'hpr_p'),
                  get_float(name, 'hpr_r'))
        pe.setScale(get_float(name, 'scale_x'),
                    get_float(name, 'scale_y'),
                    get_float(name, 'scale_z'))

        pe.addParticles(self.load_particles('fire-particles'))
        pe.addForceGroup(self.load_force('fire-force'))

        return pe

    def load_particles(self, name):
        p0 = Particles('fire-particles')
        # Particles parameters
        p0.setFactory(get_string(name, 'factory'))
        p0.setRenderer(get_string(name, 'renderer'))
        p0.setEmitter(get_string(name, 'emitter'))
        p0.setPoolSize(get_int(name, 'pool_size'))
        p0.setBirthRate(get_float(name, 'birth_rate'))
        p0.setLitterSize(get_int(name, 'litter_size'))
        p0.setLitterSpread(get_int(name, 'litter_spread'))
        p0.setSystemLifespan(get_float(name, 'system_lifespan'))
        p0.setLocalVelocityFlag(get_int(name, 'local_velocity_flag'))
        p0.setSystemGrowsOlderFlag(get_int(name, 'system_grows_older_flag'))
        # Factory parameters
        p0.factory.setLifespanBase(get_float(name, 'lifespan_base'))
        p0.factory.setLifespanSpread(get_float(name, 'lifespan_spread'))
        p0.factory.setMassBase(get_float(name, 'mass_base'))
        p0.factory.setMassSpread(get_float(name, 'mass_spread'))
        p0.factory.setTerminalVelocityBase(get_float(name, 'terminal_velocity_base'))
        p0.factory.setTerminalVelocitySpread(get_float(name, 'terminal_velocity_spread'))
        # Renderer parameters
        p0.renderer.setAlphaMode(get_int(name, 'alpha_mode'))
        p0.renderer.setUserAlpha(get_float(name, 'user_alpha'))
        # Sprite parameters
        p0.renderer.addTextureFromFile(get_string(name, 'texture'))
        p0.renderer.setColor(Vec4(get_float(name, 'color_r'),
                                  get_float(name, 'color_g'),
                                  get_float(name, 'color_b'),
                                  get_float(name, 'color_a')))
        p0.renderer.setXScaleFlag(get_int(name, 'x_scale_flag'))
        p0.renderer.setYScaleFlag(get_int(name, 'y_scale_flag'))
        p0.renderer.setAnimAngleFlag(get_int(name, 'anim_angle_flag'))
        p0.renderer.setInitialXScale(get_float(name, 'initial_x_scale'))
        p0.renderer.setFinalXScale(get_float(name, 'final_x_scale'))
        p0.renderer.setInitialYScale(get_float(name, 'initial_y_scale'))
        p0.renderer.setFinalYScale(get_float(name, 'final_y_scale'))
        p0.renderer.setNonanimatedTheta(get_float(name, 'nonanimated_theta'))
        p0.renderer.setAlphaBlendMethod(get_int(name, 'alpha_blend_method'))
        p0.renderer.setAlphaDisable(get_int(name, 'alpha_disable'))
        # Emitter parameters
        p0.emitter.setEmissionType(get_int(name, 'emission_type'))
        p0.emitter.setAmplitude(get_float(name, 'amplitude'))
        p0.emitter.setAmplitudeSpread(get_float(name, 'amplitude_spread'))
        p0.emitter.setOffsetForce(Vec3(get_float(name, 'offset_force_1'),
                                       get_float(name, 'offset_force_2'),
                                       get_float(name, 'offset_force_3')))
        p0.emitter.setExplicitLaunchVector(Vec3(get_float(name, 'explicit_launch_vector_1'),
                                                get_float(name, 'explicit_launch_vector_2'),
                                                get_float(name, 'explicit_launch_vector_3')))
        p0.emitter.setRadiateOrigin(Point3(get_float(name, 'radiate_origin_1'),
                                           get_float(name, 'radiate_origin_2'),
                                           get_float(name, 'radiate_origin_3')))
        # Disc parameters
        p0.emitter.setRadius(get_float(name, 'radius'))

        return p0

    def load_force(self, name):
        f0 = ForceGroup('fire-force')
        # Force parameters
        force0 = LinearVectorForce(Vec3(get_float(name, 'linear_vector_force_1'),
                                        get_float(name, 'linear_vector_force_2'),
                                        get_float(name, 'linear_vector_force_3')),
                                        get_float(name, 'linear_vector_force_4'),
                                   get_int(name, 'linear_vector_force_5'))
        force0.setVectorMasks(get_int(name, 'vector_masks_1'),
                              get_int(name, 'vector_masks_2'),
                              get_int(name, 'vector_masks_3'))
        force0.setActive(get_int(name, 'active'))
        f0.addForce(force0)

        return f0
```

Functions to load and read configuration from an ini file.
```
_config = {} # The configuration dictionary

# Load the configuration from the given file
def load_config(file):
    config_parser = ConfigParser.ConfigParser() # Create a ConfigParser
    config_parser.read(file) # Read the ini file
    # Store in the '_config' dictionary the options
    for section in config_parser.sections():
        _config[section] = {}
        for option in config_parser.options(section):
            _config[section][option] = config_parser.get(section, option)

# These functions returns an option value given its section, for example 'fire-particles',
# and its name, for example 'pool_size'. 
def get_string(section, option):
    return str(_config[section][option])

def get_int(section, option):
    return int(_config[section][option])

def get_float(section, option):
    return float(_config[section][option])
```

Create the World class and enter the Panda3D main loop.
```
w = World()
run()
```

Screenshots:

> Fire
> > ![http://img99.imageshack.us/img99/3003/firehf6.png](http://img99.imageshack.us/img99/3003/firehf6.png)


> Wapor
> > ![http://img264.imageshack.us/img264/7103/waportc8.png](http://img264.imageshack.us/img264/7103/waportc8.png)


> Smoke
> > ![http://img264.imageshack.us/img264/6969/smokept4.png](http://img264.imageshack.us/img264/6969/smokept4.png)


> Water
> > ![http://img264.imageshack.us/img264/1153/waterrq0.png](http://img264.imageshack.us/img264/1153/waterrq0.png)


> Fireworks
> > ![http://img259.imageshack.us/img259/2053/fireworksog2.png](http://img259.imageshack.us/img259/2053/fireworksog2.png)
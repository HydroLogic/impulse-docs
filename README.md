#Impulse

  Create animations that flow naturally from the user's movements.

  Rather than animating properties for a set amount of time,
impulse takes a start position, end position, and velocity.

##example

```
var physicsObject = Impulse(el)
  .style({
    translate: function(x, y) { return x + 'px, ' + y + 'px' },
  })
//set a starting position
phys.position(50, 50)

phys.spring({ tension: 100, damping: 10 })
  .to(100, 100).start()
```

More examples can be found [here](impulse.luster.io/physics/examples)


##Installation

####Browserify
```
npm install impulse
```

####Bower

```
bower install impulse
```
Exposes a global called Physics

####Component

```
component install luster-io/impulse
```

####Manually

Get `build/impulse.js` or `build/impulse.min.js` from github.

```html
  <script src="/scripts/impulse.js"></script>
```

Exposes a global called impulse

##High Level Explanation

  Calling impulse on an element or set of elements returns a PhysicsObject.
A physics object maintains it's own position and velocity.  You can interact
with a PhysicsObject (drag, pan, etc), and animate it.  Animations take
the current position and velocity of the PhysicsObject as a starting point, and
animate to a user defined position.

  This makes the animations flow naturally from the user's actions.

  For example a user can drag an element around.  Once they're done dragging,
the next animation will start from the position and velocity that they left off.
Making the animation feel like a natural extension of their movement.

##Documentation

Documentation can be found [here](labs.luster.io/physics/examples)

###Impulse(els)

```
var menu = Impulse($('.myMenu'))
var scroller = Impulse(document.querySelector('.myScroller'))
```

  Initializes a new `impulse` object with a single element or
an array-like collection of elements (jQuery, NodeList, etc).

###Impulse(renderFn)

```
var custom = Impulse(function(position) {
  //maybe pass the position to Facebook React, Ember, or d3 here.
})
```

  Initializes a new physics object with a render function.  Whenever
there is a change in the state of the physics object, the render function
will be called.

  PROTIP: This may be called more often than you actually want to draw to
the page, so make sure your renderer will do debounce the updates.

### imp.style(property, valueFn)

```
impulse.style('translateX', function(x, y, index) { return x + 'px' })
```

  Sets the css property in the first argument to the value returned by the function
in the second, for every element passed into the constructor, every time the
physics object's position changes. If you set a custom render function in the
constructor this method doesn't do anything, since you're manually rendering
yourself.

  The first argument to the `valueFn` is an object with positions {x, y}, the
second argument is the index of the element, since the function is called
once for each item in the collection of elements.

### impulse.style(cssObj)

  Style can also be an object of the style `{ cssProperty: valueFn,
anotherProp: anotherValFn }`.

### impulse.position()

  Gets the current position {x, y} of the physics object.

### impulse.velocity()

  Get's the current velocity of the object, if an animation is running, this
will be the current velocity from the animation.

  Get's the current velocity of the object, when interacting with the physics
object (manually setting it's position) this velocity is kept up to date by
calls to position.

##Animations

###Spring

####Options:
  * **tension:** the spring's tension (default: 100)
  * **damping:** the springs damping (default: 10)
```
var animation = impulse.spring({ tension: 100, damping: 10 })
```

### impulse.spring(opts)

  Springs from the object's current position, to x, y at the object's current
velocity.


# Accelerate

Options:
  bounce: (default: true)
  damping: amount to damp the velocity on each bounce
  minBounceHeight: 100 (default: 100)
  acceleration: { x, y } how fast to accelerate in each of the y, x

### phys.accelerate(opts)

  Returns a promise that will resolve when the physics object reaches the point x, y.

## deccelerate

### phys.deccelerate

  Returns a promise that will resolve when the physics object has moved past point x, y.
Unless stopAtEnd is false, in which case it will never resolve, only reject if phys.stop
is called.

### phys.deccelerate(x, y, opts)
  Deccelerate towards x, y starting from the current position, and at the current velocity.


###animation.to(x, y)
###animation.to({ x, y })

   This sets the position that the animation is moving towards.  This defaults to
the current position of the physics object.

###animation.from(x, y)
###animation.from({ x, y })

   This sets the position that the animation starts at.  This defaults to
the current position of the physics object.

###animation.velocity(scalar)
###animation.velocity(x, y)
###animation.velocity({x, y})

  sThis sets the initial velocity for the animation.  When only a scalar (Number) is passed in, it will default to moving in the direction from the start to the end position.  If not called, the velocity defaults to the current velocity of the physics object.

###animation.start()

   Starts the animation running.  This will cancel any other running animations.
This method is bound to `animation`, so you can conveniently pass it around without having to manually bind it.

## phys.attachSpring(attachment, opts)

  The `attachSpring` method works differently than other animation methods, in that it is constantly running, and responds to updates to the position of it's `attachment`.

Options:
  tension: the spring's tension (default: 100)
  damping: the springs damping (default: 10)
  seperation: distance to maintain from attachment, if closer than `seperation` it will spring
  away, if further away it will spring towards it.
  offset: { x, y } offset from attachment's position

  `attachment` can be another physics object, or it can be a function that will return a position { x, y } when called.

  Attaches a spring to a physics object or function `attachment`. If `attachment` is a function, it should return always return the current { x, y } positions
of the thing you are attaching to.

  Returns an `attachedSpring`, whose position and velocity can be updated as the animation is running.

```
var attachedSpring = phys.attachSpring(attachment, opts)
```

###attachedSpring.end()

  Stops the spring from running.

###attachedSpring.position()

  Updates the position of the spring.  If the spring is running, this will
affect the simulation.

###attachedSpring.velocity(x, y)

  Updates the velocity of the spring in flight.  If the spring is running, this will affect the simulation.

## phys.interact()

  If you want to allow a user to interact with a physics object, i.e. drag it around.  This will update the renderer with the position set by interact, and will record the final velocity.

###interaction.start()

  Starts the interaction, returns a promise that will fulfill when end is called
or reject when the interaction is cancelled.

###interaction.cancel()

  Stops the interaction and rejects the promise returned by start.

###interaction.position(x, y)
###interaction.position({ x, y })

  Updates the position of the physics object.  This position, along with the time it occured will be used to calculate the velocity of the physics object.

###interaction.end()

  Ends the interaction.  You can do this on touchend.  Returns a promise fulfilled with the final state of the interaction.



#TODO

  Add a `drag` method that handles the events for you.

  Throw error if using renderer and no styles are defined.

  CSS animation generator.  Sometimes you want a spring or acceleration animation, but you want it always starts with the same initial velocity and position.  In this case it would be much smarter to generate a css keyframe animation, and cache it.

  Performance instrumenting.  Use something like, http://google.github.io/tracing-framework/, to get better insights into where the performance bottlenecks are.  The goal would be to be able to simulate 100's of springs
  without any performance hit.

  Angular rotation.  Add a way to do angular rotation.

  More examples.  The more examples the better.  If you have an idea for an example you'd like to see created, create an issue.  If there's something you'd like to see, but you don't know if it's possible, create an issue!

  Use analytical solution for acceleration and deceleration.  There's may be no need to run a physics simulation for acceleration and deceleration, since these are pretty easily computed analytically, which would be much faster than numeric integration.

  Accumulator timestep with alpha smoothing.  Instead of just using whatever time the browser throws at us we should have a fixed timestep, integrate over those steps, and then linearly interpolate between states with unused time at each render, i.e. set timestep to Yms, update every Xms, and interpolate any extra time.

##LICENSE
 MIT -- Read LICENCE

# Profiles

A [functional programming](https://en.wikipedia.org/wiki/Functional_programming) extension which parses the event in
```grabber.performInteraction(BogusEvent event)```
to define _Shortcut_ to _Method_ bindings. To set it up just override
the _grabber_ ```performInteraction``` method like this:

```java
@Override
public void performInteraction(BogusEvent event) {
    profile.handle(event);
}
```

## BIAS
### Grabbers: Profile

Profiles allow the following simple dialect:

```java
grabber.setMotionBinding(LEFT, "callback_method");
```

```java
grabber.setKeyBinding('x', "callback_method");
```

```java
grabber.setKeyBinding(SHIFT, 'y', "callback_method");
```

```java
grabber.setClickBinding(RIGHT, '2', "callback_method");
```


## Example

To parse the event in ```grabber.performInteraction(BogusEvent event)```
to define _Shortcut_ to _Method_ bindings we attach a [profile](https://github.com/nakednous/bias/blob/master/src/remixlab/bias/ext/Profile.java) to the grabber, which will then allow the following high-level simple syntax:

```java
// bind the left mouse gesture to the callback method:
grabber.profile.setBinding(new MotionShortcut(LEFT), "callback_method");
// bind the 'x' key to the callback method:
grabber.profile.setBinding(new KeyboardShortcut('x'), "callback_method");
// bind the 1-right click mouse button to the callback method:
grabber.profile.setBinding(new ClickShortcut(RIGHT, 1), "callback_method");
```
In this tutorial we will:

1. Set up a grabber profile
1. Update our mouse agent (the one introduced at the [[previous tutorial|1.1.-SimpleCallback]]) to support profiles

## Grabber profile setup

To attach a profile to a grabber simply instantiate a Profile passing to its constructor a reference to the grabber: ```Profile profile = new Profile(this)```. The profile powerful API may now be used to actually define some grabber bindings such as we've done the the Ellipse implementation:

```java
  public void setMouseDragBindings() {
    profile().removeBindings();
    profile().setBinding(new MotionShortcut(LEFT), "setPosition");
    profile().setBinding(new MotionShortcut(RIGHT), "setShape");
    profile().setBinding(new ClickShortcut(LEFT, 1), "setColor");
  }
  
  public void setMouseMoveBindings() {
    profile().removeBindings();
    profile().setBinding(new MotionShortcut(NO_BUTTON), "setPosition");
    profile().setBinding(new MotionShortcut(RIGHT), "setShape");
  }
```

Finally, the grabber performInteraction should be overriden to make the profile handle the event like this:

```java
  @Override
  public void performInteraction(BogusEvent event) {
    profile().handle(event);
  }
```

## Profile-enabled mouse agent

Motion agents (like the mouse) should register gesture and click ids using the Profile static methods ```registerMotionID``` and ```registerClickID```:

```java
  public static int LEFT_ID, CENTER_ID, RIGHT_ID, WHEEL_ID, NO_BUTTON;
  public MouseAgent(InputHandler handler) {
    super(handler);
    LEFT_ID = Profile.registerMotionID(37, this.getClass(), 2);
    CENTER_ID = Profile.registerMotionID(3, this.getClass(), 2);
    RIGHT_ID = Profile.registerMotionID(39, this.getClass(), 2);
    WHEEL_ID = Profile.registerMotionID(8, this.getClass(), 1);
    NO_BUTTON = Profile.registerMotionID(BogusEvent.NO_ID, this.getClass(), 2);
    // click ids are anonymous (since they are the same as motions)
    Profile.registerClickID(LEFT_ID, this.getClass());
    Profile.registerClickID(CENTER_ID, this.getClass());
    Profile.registerClickID(RIGHT_ID, this.getClass());
  }
```

Note that for the ```LEFT_ID```, ```RIGHT_ID``` and ```CENTER_ID``` ids we used Processing ```LEFT``` (37), ```RIGHT``` (39) and ```CENTER``` (3) values, so they can safely be used interchangebly.

<!---
You may also run the example online using tersehandling.js [here](http://otrolado.info/local/tersehandling-latest/BIAS.js/TuplesTerse/war/TuplesTerse.html) (documentation coming soon).
You may also run the example online using tersehandling.js [here](http://otrolado.info/local/tersehandling-latest/BIAS.js/BoringClickAndDrag/war/Boring.html) (documentation coming soon).
-->



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
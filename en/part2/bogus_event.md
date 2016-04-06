# Bogus Events

## Bias: BogusEvents

Bogus event in that it is a high-level (soft) event which should be reduced from a low-level event.

## BIAS: BogusEvents
### Shortcuts

> A gesture identifier

Example: The mouse button + modifier mask when a dragging gesture is taking place

## BIAS: BogusEvent
### MultiTempi

* ```fired()```: event instantiation
* ```flushed()```: event termination
* ```!fired() && !flushed()```: event execution (default state)

## BIAS: BogusEvent
### Types

_BogusEvent_ instances are of the following types:

 * KeyboardEvent -> 't', 'U', or with modifiers: Shift + 'w'
 * ClickEvent: -> aka "tap", 1,2,...n clicks. Supports modifiers
 * MotionEvent -> kinematics & DOFs, abs/rel, speed
   * DOF1Event
   * DOF2Event
   * DOF3Event
   * DOF6Event
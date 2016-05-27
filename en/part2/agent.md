# Agents

## Bias: Agent
### BogusEvent reduction


## Bias: Agent
#### updateTrackedGrabber(): Update the 'inputGrabber()'


## Bias: Agent
#### handle(): perform an interaction on the 'inputGrabber()'

## Full Feature Example
(TODO: really need to decide where all the examples should go)

To implement a simple callback we will:

1. Implement a simple **BIAS** Agent, which transforms [Processing mouse events](http://processing.org/reference/javadoc/core/processing/event/package-summary.html) into **BIAS** bogus events.
1. Implement a grabber object, define its behavior, and register it at the agent.
1. Handle agents and events by calling `inputHandler.handle()` in the main event loop.

## Implementing a simple **BIAS** Agent

Every **BIAS** `Agent` holds a pool of input `grabbers`, i.e., high-level objects which can react to user input, only one of which may grabbed the agent at a given time (or none). So here we begin by implementing our first **BIAS** `Agent`, which will be responsible for:

1. Define the gesture *ids* the agent will support.
1. Transforming **Processing** `MouseEvents` into **BIAS** `DOF2Events`.
1. Defining which **BIAS** event will update the grabber in the Agent pool.
1. Defining which **BIAS** event will actually trigger an action to be performed by the grabber (if there's one selected).

```java
public class MouseAgent extends Agent {
  // 1. Gesture ids. 
  // Note that a mouse move or drag gesture is treated similarly, but with different ids
  public static final int LEFT_ID  = PApplet.LEFT, CENTER_ID = PApplet.CENTER, RIGHT_ID = PApplet.RIGHT, WHEEL_ID = MouseEvent.WHEEL, NO_BUTTON = BogusEvent.NO_ID;
  
  protected DOF2Event  currentEvent, prevEvent;
  protected boolean    move, press, drag, release;
  
  public MouseAgent(InputHandler handler) {
    super(handler);
  }
  
  // 2. Event transformation
  public void mouseEvent(processing.event.MouseEvent e) {
    move = e.getAction() == processing.event.MouseEvent.MOVE;
    press = e.getAction() == processing.event.MouseEvent.PRESS;
    drag = e.getAction() == processing.event.MouseEvent.DRAG;
    release = e.getAction() == processing.event.MouseEvent.RELEASE;
    //better and more robust is to work without modifiers, which Processing don't report reliably
    if (move || press || drag || release) {
      currentEvent = new DOF2Event(prevEvent, e.getX(), e.getY(), BogusEvent.NO_MODIFIER_MASK, move ?
      BogusEvent.NO_ID : e.getButton());
      // 3. Update the track grabber from a mouse move gesture
      // when the picking policy isn't click-to-pick
      if(move)
        updateTrackedGrabber(currentEvent);
      // 4. Send the bogus event to the current agent.inputGrabber()
      // we do this for all mouse events
      handle(press ? currentEvent.fire() : release ? currentEvent.flush() : currentEvent);
      // cache the current-event so that we pass it to the next event
      prevEvent = currentEvent.get();
      return;
    }
    if (e.getAction() == processing.event.MouseEvent.WHEEL) {// e.getAction() = MouseEvent.WHEEL = 8
      // 4. Send the (wheel) bogus event to the current agent.inputGrabber()
      handle(new DOF1Event(e.getCount(), BogusEvent.NO_MODIFIER_MASK, WHEEL_ID));
      return;
    }
    if (e.getAction() == processing.event.MouseEvent.CLICK) {
      ClickEvent bogusClickEvent = new ClickEvent(e.getX(), e.getY(),
      BogusEvent.NO_MODIFIER_MASK, e.getButton(), e.getCount());
      // 4. Send the (click) bogus event to the current agent.inputGrabber()
      handle(bogusClickEvent);
      return;
    }
  }
}
```

## Implementing a simple grabber object

Implementing a grabber object requires either to implement the `Grabber` interface, or to derive from `GrabberObject`,
which is a default implementation of that interface provided for convenience, and to implement its two abstract methods:
`checkIfGrabsInput(BogusEvent event)`, and `performInteraction(BogusEvent event)`. Whilst the former will check if
the object grabs the agent, the latter will attempt to perform an action on the event, provided the object is grabbing the agent.

```java
public class Ellipse extends GrabberObject {
  ...
  @Override
  public boolean checkIfGrabsInput(DOF2Event event) {
    return checkIfGrabsInput(event.x(), event.y());
  }
  
  @Override
  public boolean checkIfGrabsInput(ClickEvent event) {
    return checkIfGrabsInput(event.x(), event.y());
  }
  
  // low-level picking method, called by both (click and dof2 events) event handlers
  public boolean checkIfGrabsInput(float x, float y) {
    return(pow((x - center.x), 2)/pow(radiusX, 2) + pow((y - center.y), 2)/pow(radiusY, 2) <= 1);
  }
  
  @Override
  public void performInteraction(ClickEvent event) {
    // to perform an interaction we parse the event's id
    if ( event.id() == LEFT )
      setColor();
    ...
  }

  @Override
  public void performInteraction(DOF2Event event) {
    // to perform an interaction we parse the event's id
    if ( event.id() == LEFT )
      setPosition();
    ...
  }
}
```

## Handling agents

Finally, we declare a `MouseAgent`, an `InputHandler` object, register our agent at the handler, and call `inputHandler.handle()` at the main event loop.

```java
// Declare the MouseAgent object, just defined above
MouseAgent agent;
// Declare an InputHandler object
InputHandler inputHandler;
    
void setup() {
  ...
  // Instantiate the InputHandler object
  inputHandler = new InputHandler();
  // Instantiate the MouseAgent.
  // Notice that the agent will automatically be added to the InputHandler object.
  agent = new MouseAgent(inputHandler);
  registerMethod("mouseEvent", agent);
  ...
}
    
void draw() {
  ...
  // Call the inputHandler handle() method at the end of the main drawing loop.
  inputHandler.handle();
}
```

<!---
You may also run the example online using tersehandling.js [here](http://otrolado.info/local/tersehandling-latest/BIAS.js/BoringClickAndDrag/war/Boring.html) (documentation coming soon).
-->

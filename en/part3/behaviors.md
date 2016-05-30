# Behaviors

```java
public class UserObject {
  public GenericFrame gFrame = new GenericFrame(scene);
  public void draw() {
    gFrame.scene().pushModelView();
    gFrame.applyWorldTransformation();
    drawUserObject();
    gFrame.scene().popModelView();
  }
}
```

## Default motion Behaviors

### Style 1

```java
@Override
protected void performInteraction(DOF2Event event) {
  if(event.id() == LEFT)
    rotate(event);
  if(event.id() == RIGHT)
    translate(event);
}
```

### Style 2

```java
frame.setMotionBinding(LEFT, "translate");
frame.setMotionBinding(RIGHT, "scale");
```java```java

## Custom motion behaviors

### Style 1

Extend the generic frame example and implement some behaviors

### Style 2

```java
frame.setMotionBinding(this, LEFT, "boxCustomMotion");
frame.setClickBinding(this, LEFT, 1, "boxCustomClick");
```

```java
public void boxCustomMotion(InteractiveFrame frame, MotionEvent event) {
  frame.screenRotate(event);
}
```

```java
public void boxCustomClick(InteractiveFrame frame) {
  frame.center();
}
```

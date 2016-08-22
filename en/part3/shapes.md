# Shapes

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

## InteractiveFramesPShapes

## InteractiveFrame graphics procedures

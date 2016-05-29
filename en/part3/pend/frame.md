# Frames

```java
  frame.setReferenceFrame(parent);
```

## Dandelion
### Frame: Hierarchy

```java
  Frame frame = new Frame();
  scene.pushModelView();
  scene.applyModelView(frame.matrix());
  // Draw your object here, in the local fr coordinate system.
  scene.popModelView();
```
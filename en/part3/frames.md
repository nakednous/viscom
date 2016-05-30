# Frames

```java
    Frame frame = new Frame();
    frame.setPosition(new Vec(0.5,0,0));
    frame.setOrientation(new Quat(new Vec(0,1,0), new Vec(1,1,1)));
    scene.pushModelView();
    scene.applyModelView(frame.matrix());
    // Draw your object here, in the local frame coordinate system.
    scene.popModelView();
```

frame_hierarchy.mermaid (only by means of keeping a reference to ```referenceFrame()```)
Link me to: active vs passive transformations & stack of transformations

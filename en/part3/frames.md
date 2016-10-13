# Frames

## Introduction

Geometry transformations allow to control de view-point and interact with the objects within a virtual environment. A given geometry transformation of a point defined within a coordinate system can be understood in two different but equivalent ways: either the physical position of the point is transformed while the coordinate system remains fixed, or the coordinate system changes (also referred to as _change of basis_[^1]) while the point remains fixed. The former interpretation sometimes is referred to as _active_ or _alibi_ transformation while the second is referred to as _passive_ or _alias_ transformation[^2].

Active transformations are more suitable to handle rigid body transformations (i.e., a body where the distances between every pair of points remains always the same). Passive transformations allow to represent the relative motion of an articulated rigid body set where each element lies in its own _local_ coordinate system. In the latter case, the coordinate systems form a rooted tree-hierarchy that can easily be traversed by means of a matrix stack of transformations[^3]:

```java
void traverse(CoordinateSystem sys) {
  pushMatrix();
  applyMatrix(sys.matrix());
  sys.visitCallback();
  for (CoordinateSystem child : sys.children())
    traverse(child);
  popMatrix();
}
```

and then just calling ```traverse(root)``` on the root coordinate system. Note in the pseudo-code above that the ```CoordinateSystem``` can be represented by a single transformation matrix ($$M$$).

## Interactive Frames

In Proscene an _InteractiveFrame_ (or simply a _frame_) is a 2D or 3D coordinate system encapsulating an _alias_ _angle preserving_ transformation ($$M(frame)$$) sequence of a ```translation``` ($$T(\delta x,\delta y)$$), a ```rotation``` ($$R(\beta)$$) and a uniform, non-negative ```scaling``` ($$S(\lambda)$$). These individual transformations are defined respect to a _reference frame_ which is just another frame instance that may be ```null```, when it stands for the world coordinate system.

Since geoemtry transformations don't commute generally the order of these transformations is important: the frame is first translated, then rotated around the new translated origin and then scaled, i.e., $$M(frame)=T(\delta x,\delta y)R(\beta)S(\lambda)$$. Mnemonically, this transformation sequence may be read it from right-to-left too as: the interactive-frame is first scaled, then rotated around the reference frame origin and then translated respect to the reference frame origin.

To define a frame simply pass the ```scene``` object to its constructor:

```java
Scene scene;
InteractiveFrame frame;
void setup() {
  ...
  scene = new Scene(this);
  InteractiveFrame frame = new InteractiveFrame(scene);
  frame.setTranslation(new Vec(0.5,0,0));
  frame.setRotation(new Quat(new Vec(0,1,0), new Vec(1,1,1)));
  frame.setScaling(2);
}
```

The frame ```matrix``` method will then be useful to render an object onto the screen, as follows:

```java
void draw() {
  pushMatrix();
  scene.applyModelView(body.matrix());
  // Draw your object here, in the local frame coordinate system.
  popMatrix();
}
```

## Hierarchy of Frames

Frames can hence easily be organized in a rooted tree hierarchy, which root is the world coordinate system, just by calling ```setReferenceFrame(Frame)```. This method prevents the creation of loops in the hierarchy and gracefully inform about if it's going to happen.

The default ```referenceFrame()``` is the world coordinate system (represented by a ```null``` ```referenceFrame()```). If you ```setReferenceFrame(Frame)``` to a different frame, you must then discern:

1. The *local* ```translation()```, ```rotation()``` and ```scaling()``` , defined with respect to the ```referenceFrame()```.
2. The *global* ```position()```, ```orientation()``` and ```magnitude()```, always defined with respect to the world coordinate system.

This terminology for *local* (```translation()```, ```rotation()``` and ```scaling()```) and *global* ( ```position()```, ```orientation()``` and
```magnitude()```) definitions is used in all the frame method prototypes and should be sufficient to prevent ambiguities. These notions are obviously identical when the ```referenceFrame()``` is ```null```, i.e., when the frame is defined in the world coordinate system.

As an illustration...

```java
Scene scene;
InteractiveFrame body, leftArm, rightArm;
void setup() {
  ...
  scene = new Scene(this);
  InteractiveFrame body = new InteractiveFrame(scene);
  InteractiveFrame leftArm = new InteractiveFrame(scene);
  InteractiveFrame rightArm = new InteractiveFrame(scene);
  leftArm.setReferenceFrame(body);
  rightArm.setReferenceFrame(body);
}
```

frame_hierarchy.mermaid (only by means of keeping a reference to ```referenceFrame()```)
![Frame hierarchy](fig/frame_hierarchy.mermaid.png)

Render the tree hierarchy onto the screen, as follows:

```java
void draw() {
  pushMatrix();  
  scene.applyModelView(body.matrix());
  drawBody();
  pushMatrix();
  scene.applyTransformation(leftArm);// same as scene.applyModelView(leftArm.matrix());;
  drawArm();
  popMatrix();
  pushMatrix();
  rightArm.applyTransformation();// same as scene.applyTransformation(leftArm);
  drawArm();
  popMatrix();
  popMatrix();
}
```

While a ```pushMatrix``` followed by one of the (equivalent) apply transformation methods (```Scene.applyModelView(Mat)```, ```Scene.applyTransformation(InteractiveFrame)``` or simply ```InteractiveFrame.applyTransformation()```) calls mean _entering_ into the frame coordinate system, a ```popMatrix``` means _leaving_ it and returning to its reference frame.

## Point transformations

Many functions are provided to transform a point from one coordinate system (Frame) to an other: see ```coordinatesOf(Vec)```, ```inverseCoordinatesOf(Vec)```, ```coordinatesOfIn(Vec, Frame)```, ```coordinatesOfFrom(Vec, Frame)```...
 
## Vector transformations

You may also want to transform a vector (such as a normal), which corresponds to applying only the rotational part of the frame transformation: see ```transformOf(Vec)``` and ```inverseTransformOf(Vec)```.

The ```translation()```, ```rotation()``` and uniform positive ```scaling()``` that are encapsulated in a Frame can also be used to represent an angle preserving transformation of space. Such a transformation can also be interpreted as a change of coordinate system, and the coordinate system conversion functions actually allow you to use a Frame as an angle preserving transformation. Use ```inverseCoordinatesOf(Vec)``` (resp. ```coordinatesOf(Vec)```) to apply the transformation (resp. its inverse). Note the inversion.

## Constraints

An interesting feature of Frames is that their displacements can be constrained. When a ```Constraint``` is attached to a Frame, it filters the input of ```translate(Vec)``` and ```rotate(Rotation)```, and only the resulting filtered motion is applied to the Frame. The default ```constraint()``` ```null```
resulting in no filtering. Use ```setConstraint(Constraint)``` to attach a Constraint to a frame.

Classical constraints are provided for convenience: 

1. ```LocalConstraint```
2. ```WorldConstraint```
3. ```EyeConstraint```

and new constraints can very easily be implemented.

## Derived classes

The ```GenericFrame``` class inherits Frame and implements
all sorts of motion actions, so that a Frame (and hence an object) can be manipulated
in the scene by whatever user interaction means you can imagine.

[^1]: [Change of Basis](https://en.wikipedia.org/wiki/Change_of_basis)
[^2]: [3D Math Primer for Graphics and Game Development](http://gamemath.com/)
[^3]: [Transformations Processing Tutorial](https://www.processing.org/tutorials/transform2d/)

# Part III: Interaction

We spend most of our lifes in a 3D world and can easily move on the surface of the planet and also interact with the objects around us, sometimes even without noticing it. Through special purpose equipment and training the development of aviation, has enabled us to conquer the third dimension. However, when it comes to interact within a virtual environment, not matter it is in 2D, 2.5D (e.g., terrain navigation) or 3D, we find it difficult and can easily get lost or lost track of the objects that first attracted our attention. This is mainly due to poor interface design, wrong selection of the hardware used to track user input, wrong selection of the interaction technique or a combination of some of them.

The two main interaction tasks within virtual environments are:

1. _Navigation_ which deals with viewpoint manipulation; and
2. _Picking & manipulation_ which has to do with object selection and manipulation.

To assist programming these two key interaction tasks and tackle some of the difficulties mention above, we developed the _Proscene_ Processing library.

## Proscene use

All _Proscene_ sketches require a `Scene` object to be instantiated, usually within the sketch Processing ```setup()``` method. Depending whether the scene is onscreen or offscreen, there are two ways to achieve this:

1. Onscreen scenes:

  ```java
  Scene scene;
  void setup() {
     size(1280, 800, P3D);
     scene = new Scene(this);
  }
  ```

2. Offscreen scenes:

   ```java
   PGraphics canvas;
   Scene scene;
   void setup() {
     canvas = createGraphics(1280, 800, P3D);
     scene = new Scene(this);
   }
   ```

in both cases a 3D scene will be created, not only having default interactivity through the mouse or touchscreen (if the sketch is to be run in Android mode) and the keyboard, but also also exposing other _Proscene_ features which soon we will be studying. Note that a 2D scene may be created by simply passing `P2D` or `JAVA2D` to the `size()` or `createGraphics()` functions instead.

We will use the Proscene library to deal with all sort of interaction techniques throughout this part of the book which is organized as followed:

* [Frames](frames.md) introduces _Proscene_ coordinate systems.
* [Shapes](shapes.md) deals with the set up of a frame shape.
* [Picking](picking.md) deals with frame selection.
* [Behaviors](behaviors.md) discusses how to bind default or custom behaviors to a frame.
* [Input](input.md) Discusses how user input may be gathered from any [Human Interface Device](https://en.wikipedia.org/wiki/Human_interface_device) (HID).
* [Eye](eye.md) deals with eye frames and introduces various eye attributes.
* [Animation](animation.md) presents various animation techniques.

# Animators

There are two mechanisms to implement an animation in **FPSTiming**:

1. Implement the [Animator](http://otrolado.info/fpstimingApi/remixlab/fpstiming/Animator.html) interface, or extend from an [AnimatorObject](http://otrolado.info/fpstimingApi/remixlab/fpstiming/AnimatorObject.html) (which provides a default implementation of that interface) and implement its [animate()](http://otrolado.info/fpstimingApi/remixlab/fpstiming/AnimatorObject.html#animate()) method.
2. Register an external animation handler method and invoke it through [reflection](http://docs.oracle.com/javase/tutorial/reflect/).

Here we will implement a simple [2D particle system](http://en.wikipedia.org/wiki/Particle_system) as follows:

1. Implement a `ParticleSystem` from extending an [AnimatorObject](http://otrolado.info/fpstimingApi/remixlab/fpstiming/AnimatorObject.html) and overriding its [animate()](http://otrolado.info/fpstimingApi/remixlab/fpstiming/AnimatorObject.html#animate()) function.
2. Implement a `Particle2D` object representing each particle's individual behavior.
3. Instantiate and draw the `ParticleSystem`.

## Implementing a ParticleSystem from an ObjectAnimator and overriding its animate() function

Our `ParticleSystem` will only hold the `Particle2D` set (see the next section), so we declare it and instantiate it as follows:

```java
class ParticleSystem extends AnimatorObject {
  int nbPart;
  Particle2D[] particle;

  public ParticleSystem() {
    nbPart = 2000;
    particle = new Particle2D[nbPart];
    for (int i = 0; i < particle.length; i++)
      particle[i] = new Particle2D();
  }
  ...
}
```

We override the `ObjectAnimator.animate()` function (the default implementation is empty) to loop through the particle set
calling each particle's `own animate()` function:

```java
class ParticleSystem extends AnimatorObject {
  ...
  @Override
  public void animate() {
    for (int i = 0; i < nbPart; i++)
      if (particle[i] != null)
        particle[i].animate();
  }
}
```

## Implementing a 2d Particle object

The `Particle2D` class implements the actual animation of the system. Here we implement a very simple simulation:


```java
public class Particle2D {
  public PVector speed;
  public PVector pos;
  public int age;
  public int ageMax;

  public Particle2D() {
    speed = new PVector();
    pos = new PVector();
    init();
  }

  public void animate() {
    speed.z -= 0.05f;
    pos = PVector.add(pos, PVector.mult(speed, 10f));

    if (++age == ageMax)
      init();
  }

  public void draw() {
    stroke( 255 * ((float) age / (float) ageMax), 255 * ((float) age / (float) ageMax), 255);
    vertex(pos.x, pos.y);
  }

  public void init() {
    pos = new PVector();
    float angle = 2.0f * PI * random(1);
    float norm = 0.04f * random(1);
    speed = new PVector(norm * cos(angle), norm  * sin(angle));
    age = 0;
    ageMax = 50 + (int) random(100);
  }
}
```

It's worth noticing that each particle implements its own drawing method too. We'll using it within our `PApplet` to visualize the simulation
(see the next section).

## Particle system instantiation and drawing

At the `PApplet` we declare a `ParticleSystem` instance and a `TimingHandler` instance. They are properly
instantiated at the `PApplet.setup()` function. We also start the system animation within that function:


```java
ParticleSystem system;
TimingHandler handler;

public void setup() {
  size(640, 360, P2D);
  system = new ParticleSystem();
  handler = new TimingHandler(system);
  system.startAnimation();
}
```

To visualize our simulation we override the `PApplet.draw()` function to: 1. Loop through the system's particle set,
calling `Particle2D.draw()` on each particle; and, 2. Call the `TimingHandler.handle()` method:


```java
public void draw() {
  ...
  for (int i = 0; i < system.particle.length; i++) {
    system.particle[i].draw();
  }
  ...
  handler.handle();
}
```

Finally, to control the start/stop/toggle the animation we override the `PApplet.keyPressed()` function as follows:

```java
public void keyPressed() {
  if (key == '+')
    system.setAnimationPeriod(system.animationPeriod()-2);
  if (key == '-')
    system.setAnimationPeriod(system.animationPeriod()+2);
  if (key == ' ')
    system.toggleAnimation();
}
```
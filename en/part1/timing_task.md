# Timing Tasks

In this lesson we will display serveral ellipses and implement some **FPSTiming**
[timing tasks](http://otrolado.info/fpstimingApi/remixlab/fpstiming/TimingTask.html) to control their behavior. We will:

1. Implement the Ellipse class.
2. Define/instantiate some **FPSTiming** `TimingTasks` to affect the Ellipses' properties in the future.

## Implementing the Ellipse class

The implementation of the Ellipse class is pretty straightforward. We implement two types of color/position and radii setter methods:
one to randomly set the ellipse properties (parameterless versions), and the other to set them according to the parameters passed.

```java
public class Ellipse {
  public float radiusX, radiusY;
  public PVector center;
  public color colour;
  public int contourColour;

  public Ellipse() {
    contourColour = color(random(0, 255), random(0, 255), random(0, 255));
    setColor();
    setPosition();
    setRadii();
  }

  public void setColor() {
    setColor(color(random(0, 255), random(0, 255), random(0, 255)));
  }

  public void setColor(color c) {
    colour = c;
  }

  public void setRadii() {
    float maxRadius = 50;
    setRadii(random(20, maxRadius), random(20, maxRadius));
  }

  public void setRadii(float rx, float ry) {
    radiusX = rx;
    radiusY = ry;
  }

  public void setPosition() {
    float maxRadius = 50;
    float low = maxRadius;
    float highX = width - maxRadius;
    float highY = height - maxRadius;
    setPosition(new PVector(random(low, highX), random(low, highY)));
  }

  public void setPosition(PVector p) {
    center = p;
  }

  public void draw() {
    pushStyle();
    stroke(contourColour);
    strokeWeight(4);
    fill(colour);
    ellipse(center.x, center.y, 2 * radiusX, 2 * radiusY);
    popStyle();
  }
}
```

It's worth noticing that we also implemented an Ellipse `Ellipse.draw()` method which we use to display the ellipses.

## Defining and instantiating some FPSTiming TimingTasks

At the `PApplet` instance, we first conveniently define some wrapper methods to the ellipses' properties we want to affect:

```java
void setEllipsesColor() {
  for (int i = 0; i < ellipses.length; i++)
    ellipses[i].setColor();
}

void setEllipsesPosition() {
  for (int i = 0; i < ellipses.length; i++)
    ellipses[i].setPosition();
}

void setEllipsesRadii() {
  for (int i = 0; i < ellipses.length; i++)
    ellipses[i].setRadii();
}
```

Within the `PApplet.setup()` function, we then instantiate the ellipses and implement some TimingTasks to affect the properties we just wrapped:

```java
Ellipse [] ellipses;
TimingHandler timingHandler;
TimingTask positionTask, radiiTask, colourTask;

void setup() {
  size(600, 600);
  timingHandler = new TimingHandler();
  ellipses = new Ellipse[50];
  for (int i = 0; i < ellipses.length; i++)
    ellipses[i] = new Ellipse();
  positionTask = new TimingTask() {
    @Override
    public void execute() {
      setEllipsesPosition();
    }
  };
  timingHandler.registerTask(positionTask);
  positionTask.run(1000);
  radiiTask = new TimingTask() {
    @Override
    public void execute() {
      setEllipsesRadii();
    }
  };
  timingHandler.registerTask(radiiTask);
  radiiTask.run(100);
  colourTask = new TimingTask() {
    @Override
    public void execute() {
      setEllipsesColor();
    }
  };
  timingHandler.registerTask(colourTask);
  colourTask.run(50);
}
```

Within the `PApplet.drawp()` function, we 1. Loop through the ellipses set,
calling `Ellipse.draw()` on each ellipse; and, 2. Call the `TimingHandler.handle()` method:

```java
void draw() {
  background(25, 75, 125);
  for (int i = 0; i < ellipses.length; i++)
    ellipses[i].draw();
  timingHandler.handle();
}
```

Finally, within the `PApplet.keyPressed()` function, we define the key shortcuts to toggle the `TimingTasks`.

```java
void keyPressed() {
  if (key == 'c')
    if (colourTask.isActive())
      colourTask.stop();
    else
      colourTask.run(50);
  if (key == 'p')
    if (positionTask.isActive())
      positionTask.stop();
    else
      positionTask.run(1000);
  if (key == 'r')
    if (radiiTask.isActive())
      radiiTask.stop();
    else
      radiiTask.run(100);
}
```
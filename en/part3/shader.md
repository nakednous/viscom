# Shaders in Processing

The PShader class
The PShader class introduced in Processing 2.0 and completed in 3.0 allows users to encapsulate all these stages into a single entity that exposes basic methods to set the value of uniform variables declared in the shader source. The PShader class was designed giving priority to Processing's immediate- mode rendering. The following program listing illustrates the basic use of PShader, as well as the state-based approach in Processing:

```java
PShader sh;
   void setup() {
     size(640, 360, P3D);
     sh = loadShader("frag.glsl",
                     "vert.glsl");
     shader(sh);
   }
   void draw() {
     background(180);
     fill(140, 140, 190);
     sh.set("noiseFactor", 0.1);
     sphere(50);
}
```

Uniforms & attributes
Basic shaders



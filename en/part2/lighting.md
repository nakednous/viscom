# Lighting

Lighting of a 3D scene is fundamental to convey the appearance of volume and shading of objects. Without if, objects are rendered in flat colors like, and their 3D nature could be hard to see:

[Image of flat shaded cube]



Lighting a 3D scene involves placing one or more light sources in the space, and defining their parameters, such as type (point, spotlight) and color (diffuse, ambient, specular) . We won't go into the details of lighting in OpenGL, but something important to mention here is that all the mathematical models we use to generate lights with GLSL shaders are very simple approximations to lighting in the real world. The model we will use in the following example is probably one of the simplest, and evaluates the light intensity at each vertex of the object, and then uses the built-in interpolation of the graphic pipeline to get a continuous gradient of color across the object faces. The light intensity at each vertex is computed as the dot product between the vertex normal and the direction vector between the vertex and light positions. This model represents a point light source that emits light equally in all directions:
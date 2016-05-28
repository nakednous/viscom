# Rendering and Interaction

The basic metaphor in CG is that of the graphics pipeline, the sequence of steps that use the information about the virtual scene and the user input to produce a final image on the screen.

The pipeline links interaction and rendering: interaction (camera movement, object selection, etc) is represented through tansformation matrices and vertex attributes that are used the pipeline to generate the final rendering accordingly.

Each stage in the pipeline is executed by a special type of program called shader. Processing uses a set of default shaders so users don't need to worry about the default modes of handling rendering and interaction, but can also write their own.

Input handling is also built-in, but ProScene provides mode advanced and flexible ways of handling user input.

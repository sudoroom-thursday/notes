# [[GLSL]] (OpenGL Shading Language)

GLSL (OpenGL Shading Language) is a high-level shading language designed for the OpenGL graphics API. It is used to write shaders for rendering 2D and 3D graphics, including vertex, fragment, geometry, tessellation, and compute shaders. GLSL enables developers to create custom graphics effects, lighting models, and procedural textures, among other things.

## GLSL Syntax and Structure

GLSL shares similarities with C and C++ in terms of syntax and structure. Some key features of GLSL include:

- **Data types**: GLSL supports basic data types, such as `int`, `float`, `bool`, and `double`, as well as vector and matrix types (e.g., `vec2`, `vec3`, `vec4`, `mat2`, `mat3`, `mat4`).
- **Control structures**: GLSL provides standard control structures, such as `if`, `else`, `for`, `while`, and `switch`.
- **Functions**: Developers can create custom functions in GLSL, as well as use built-in functions for common operations, such as mathematical calculations, texture sampling, and noise generation.
- **Input and output**: GLSL shaders communicate with the main application and other shaders using input and output variables, such as `attribute`, `varying`, `uniform`, and `in/out`.

## Example of a Simple GLSL Shader

Here's an example of a simple GLSL vertex and fragment shader that renders a textured object with basic diffuse lighting:

```glsl
// Vertex shader
#version 330 core

layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoord;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

out vec3 FragPos;
out vec3 Normal;
out vec2 TexCoord;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0);
    FragPos = vec3(model * vec4(aPos, 1.0));
    Normal = mat3(transpose(inverse(model))) * aNormal;
    TexCoord = aTexCoord;
}

/////////////////////////////////////////////////////////////////////////////

// Fragment shader
#version 330 core

struct Material {
    sampler2D diffuse;
    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
    float shininess;
};

struct Light {
    vec3 position;
    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
};

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;

uniform Material material;
uniform Light light;

out vec4 FragColor;

void main()
{
    vec3 norm = normalize(Normal);
    vec3 lightDir = normalize(light.position - FragPos);
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = light.diffuse * diff * vec3(texture(material.diffuse, TexCoord));

    vec3 viewDir = normalize(-FragPos);
    vec3 reflectDir = reflect(-lightDir, norm);
    float spec = pow(max(dot(viewDir, reflectDir), 0.0), material.shininess);
    vec3 specular = light.specular * spec * material.specular;

    vec3 result = (light.ambient * vec3(texture(material.diffuse, TexCoord))) + diffuse + specular;
    FragColor = vec4(result, 1.0);
}
```

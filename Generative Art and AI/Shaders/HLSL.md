# [[HLSL]] (High-Level Shading Language)

HLSL (High-Level Shading Language) is a high-level shading language designed for the DirectX graphics API. It is used to write shaders for rendering 2D and 3D graphics, including vertex, pixel (fragment), geometry, hull (tessellation control), domain (tessellation evaluation), and compute shaders. HLSL enables developers to create custom graphics effects, lighting models, and procedural textures, among other things.

## HLSL Syntax and Structure

HLSL shares similarities with C and C++ in terms of syntax and structure. Some key features of HLSL include:

- **Data types**: HLSL supports basic data types, such as `int`, `float`, `bool`, and `double`, as well as vector and matrix types (e.g., `float2`, `float3`, `float4`, `float3x3`, `float4x4`).
- **Control structures**: HLSL provides standard control structures, such as `if`, `else`, `for`, `while`, and `switch`.
- **Functions**: Developers can create custom functions in HLSL, as well as use built-in functions for common operations, such as mathematical calculations, texture sampling, and noise generation.
- **Input and output**: HLSL shaders communicate with the main application and other shaders using input and output variables, such as `cbuffer`, `tbuffer`, `StructuredBuffer`, `RWStructuredBuffer`, `Texture2D`, `SamplerState`, and `in/out`.

## Example of a Simple HLSL Shader

Here's an example of a simple HLSL vertex and pixel shader that renders a textured object with basic diffuse lighting:

```hlsl
// Vertex shader
cbuffer ConstantBuffer : register(b0)
{
    float4x4 model;
    float4x4 view;
    float4x4 projection;
};

struct VertexInputType
{
    float3 position : POSITION;
    float3 normal : NORMAL;
    float2 tex : TEXCOORD0;
};

struct VertexOutputType
{
    float4 position : SV_POSITION;
    float3 normal : NORMAL;
    float2 tex : TEXCOORD0;
};

VertexOutputType SimpleVS(VertexInputType input)
{
    VertexOutputType output;
    float4 worldPosition = mul(float4(input.position, 1.0), model);
    output.position = mul(worldPosition, view);
    output.position = mul(output.position, projection);
    output.normal = mul(input.normal, (float3x3)model);
    output.tex = input.tex;
    return output;
}

//////////////////////////////////////////////////////////////////////////

// Pixel shader
cbuffer ConstantBuffer : register(b0)
{
    float4 ambientColor;
    float4 diffuseColor;
    float3 lightDirection;
    float padding;
};

Texture2D textureMap : register(t0);
SamplerState samplerState : register(s0);

struct PixelInputType
{
    float4 position : SV_POSITION;
    float3 normal : NORMAL;
    float2 tex : TEXCOORD0;
};

float4 SimplePS(PixelInputType input) : SV_TARGET
{
    float3 light;
    float3 normal;
    float lightIntensity;
    float4 textureColor;

    normal = normalize(input.normal);
    lightIntensity = saturate(dot(normal, lightDirection));

    textureColor = textureMap.Sample(samplerState, input.tex);
    light = ambientColor.rgb;
    light += (diffuseColor.rgb * lightIntensity);
    return float4(light, 1.0) * textureColor;
}

```


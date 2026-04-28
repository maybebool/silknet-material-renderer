# Silk.NET Material Renderer

<p align="center">
  <img src="SAE.5300S1/Images/6.jpg" alt="Silk.NET Material Renderer" width="100%">
</p>

[![C#](https://img.shields.io/badge/C%23-.NET-512BD4?style=flat-square&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![OpenGL](https://img.shields.io/badge/OpenGL-4.x-5586A4?style=flat-square&logo=opengl&logoColor=white)](https://www.opengl.org/)
[![Silk.NET](https://img.shields.io/badge/Silk.NET-Low--Level_Bindings-FF6F61?style=flat-square)](https://github.com/dotnet/Silk.NET)
[![GLSL](https://img.shields.io/badge/GLSL-Custom_Shaders-77B829?style=flat-square)](https://www.khronos.org/opengl/wiki/OpenGL_Shading_Language)

A low-level OpenGL rendering engine built with Silk.NET, demonstrating manual GPU resource management for various material types through custom textures, shaders, and mesh pipelines — bypassing high-level abstractions to directly manage the full vertex processing stage.

## Objective

Explore low-level graphics programming by building a rendering pipeline from scratch using Silk.NET's direct OpenGL bindings. Each rendered model showcases a different material type (marble, chipboard, polished metal, concrete, mirror) with manually configured geometry buffers, dedicated GLSL shader programs, and custom texture pipelines. The project serves as a reference implementation for understanding the GPU buffer lifecycle without engine-level abstractions.

## Architecture

### GPU Buffer Pipeline

The rendering pipeline is built on three core OpenGL buffer objects, each serving a distinct role in the vertex processing stage:

| Buffer | Role | Purpose |
|:---|:---|:---|
| **VBO** | Vertex Buffer Object | Stores raw vertex attribute data (positions, normals, UVs) in GPU memory |
| **EBO** | Element Buffer Object | Enables indexed rendering, eliminating redundant vertex data |
| **VAO** | Vertex Array Object | Encapsulates the complete vertex attribute configuration |

#### Vertex Layout

Each vertex $v_i$ is stored as a contiguous block of position $\mathbf{p}$, normal $\mathbf{n}$, and texture coordinate $\mathbf{t}$:

$$v_i = \begin{bmatrix} p_x & p_y & p_z & n_x & n_y & n_z & t_u & t_v \end{bmatrix}$$

The stride across attributes is computed as:

$$\text{stride} = \sum_{k=1}^{A} \text{sizeof}(\text{attribute}_k)$$

#### Indexed Rendering

The EBO stores index triplets referencing the VBO, reducing memory from $O(3F)$ to $O(N + F)$ by sharing vertices between adjacent faces:

$$\text{EBO} = \{(i_0, i_1, i_2)_f \mid f \in [0, F)\}, \quad i_k \in [0, N)$$

#### Attribute Configuration

The VAO binds VBOs and EBOs together with attribute pointers. Each attribute $k$ is defined by:

$$\text{AttribPointer}(k) = \left(\text{index}, \ \text{size}_k, \ \text{type}_k, \ \text{stride}, \ \text{offset}_k\right)$$

$$\text{offset}_k = \sum_{j=0}^{k-1} \text{sizeof}(\text{attribute}_j)$$

A single `glBindVertexArray` call restores the entire vertex specification state.

### Shader Pipeline

Each material is driven by a dedicated GLSL shader program (vertex + fragment), compiled and linked at runtime. Shader uniforms control material-specific parameters such as specular intensity, reflection mapping, and texture blending.

## Materials Gallery

Each model demonstrates a distinct material pipeline with custom shader logic and texture configuration.

| Model | Material | Technique |
|:---|:---|:---|
| Skull | Marble | Diffuse + normal mapping with stone texture |
| Spiral | Chipboard | Layered wood grain texture with subtle specularity |
| Diamond | Polished Metal | High-specular reflection with environment sampling |
| Icosahedron | Concrete | Rough diffuse surface with low reflectance |
| Möbius Strip | Mirror | Reflective environment mapping |

<p align="center">
  <img src="SAE.5300S1/Images/1.jpg" alt="Skull — Marble" width="45%">
  &nbsp;&nbsp;
  <img src="SAE.5300S1/Images/2.jpg" alt="Spiral — Chipboard" width="45%">
</p>
<p align="center">
  <em>Skull (Marble) · Spiral (Chipboard)</em>
</p>

<p align="center">
  <img src="SAE.5300S1/Images/3.jpg" alt="Diamond — Polished" width="45%">
  &nbsp;&nbsp;
  <img src="SAE.5300S1/Images/4.jpg" alt="Icosahedron — Concrete" width="45%">
</p>
<p align="center">
  <em>Diamond (Polished) · Icosahedron (Concrete)</em>
</p>

<p align="center">
  <img src="SAE.5300S1/Images/5.jpg" alt="Möbius Strip — Mirror" width="45%">
</p>
<p align="center">
  <em>Moebius Strip (Mirror)</em>
</p>

## Controls

| Mode | Key | Action |
|:---|:---|:---|
| Settings Mode | `Space` | Enter observation mode |
| Observation Mode | `W` `A` `S` `D` + `LMB` | Navigate the scene |
| Observation Mode | `Space` | Return to settings mode |


## Getting Started

```bash
git clone https://github.com/maybebool/GraphicsProgramming.git
cd GraphicsProgramming
dotnet restore
dotnet run
```

**Prerequisites:** .NET SDK 6.0+, GPU with OpenGL 4.x support.

## Tech Stack

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=flat-square&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![OpenGL](https://img.shields.io/badge/OpenGL-5586A4?style=flat-square&logo=opengl&logoColor=white)](https://www.opengl.org/)
[![GLSL](https://img.shields.io/badge/GLSL-77B829?style=flat-square)](https://www.khronos.org/opengl/wiki/OpenGL_Shading_Language)

| Category | Technology |
|:---|:---|
| Runtime | .NET (C#) |
| GPU Bindings | Silk.NET — low-level .NET bindings for OpenGL |
| Shading | GLSL — custom vertex and fragment shaders per material |
| Rendering | Manual VBO/EBO/VAO management, indexed draw calls |

## Limitations & Future Work

This project focuses on the fundamentals of manual GPU buffer management and material-specific shading. Possible extensions include:

- Physically-based rendering (PBR) with Cook-Torrance BRDF
- Shadow mapping (directional + point light shadow volumes)
- Deferred rendering pipeline for multi-light scenes
- Normal mapping and parallax occlusion across all materials
- Model loading via Assimp for arbitrary mesh import
- Post-processing passes (bloom, SSAO, tone mapping)

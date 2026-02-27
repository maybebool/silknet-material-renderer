## Low level Mesh creation with Silk.net

![Silk.NET](https://github.com/maybebool/GraphicsProgramming/blob/main/SAE.5300S1/Images/6.jpg)

# Silk.NET Material Renderer

A low-level OpenGL rendering engine built with [Silk.NET](https://github.com/dotnet/Silk.NET), demonstrating manual GPU resource management for various material types through custom textures, shaders, and mesh pipelines.

## Overview

This project takes a **low-level approach** to mesh creation and rendering — bypassing high-level abstractions to directly manage the OpenGL buffer pipeline. Each model showcases a different material type, with manually configured geometry buffers and shader programs.

## Architecture

### GPU Buffer Pipeline

The rendering pipeline is built on three core OpenGL buffer objects, each serving a distinct role in the vertex processing stage:

#### Vertex Buffer Object (VBO)

Stores raw vertex attribute data (positions, normals, texture coordinates) in GPU memory. For a vertex $v_i$ with position $\mathbf{p}$, normal $\mathbf{n}$, and texture coordinate $\mathbf{t}$:

$$v_i = \begin{bmatrix} p_x & p_y & p_z & n_x & n_y & n_z & t_u & t_v \end{bmatrix}$$

The VBO holds a contiguous array of $N$ such vertices with a total stride of:

$$\text{stride} = \sum_{k=1}^{A} \text{sizeof}(\text{attribute}_k)$$

where $A$ is the number of vertex attributes.

#### Element Buffer Object (EBO)

Enables **indexed rendering** by storing index references into the VBO, eliminating redundant vertex data. For a triangle mesh with $F$ faces:

$$\text{EBO} = \{(i_0, i_1, i_2)_f \mid f \in [0, F)\}, \quad i_k \in [0, N)$$

This reduces memory usage from $O(3F)$ to $O(N + F)$ vertices, since shared vertices between adjacent faces are stored only once.

#### Vertex Array Object (VAO)

Encapsulates the complete vertex attribute configuration — binding VBOs and EBOs together with attribute pointer definitions. Each attribute $k$ is defined by:

$$\text{AttribPointer}(k) = \left(\text{index}, \ \text{size}_k, \ \text{type}_k, \ \text{stride}, \ \text{offset}_k\right)$$

where the offset for attribute $k$ is computed as:

$$\text{offset}_k = \sum_{j=0}^{k-1} \text{sizeof}(\text{attribute}_j)$$

The VAO captures this entire state, allowing the full vertex specification to be restored with a single `glBindVertexArray` call.

### Shader Pipeline

Each material is driven by a dedicated GLSL shader program consisting of a vertex shader and a fragment shader, compiled and linked at runtime.

## Controls

| Mode | Key | Action |
|---|---|---|
| **Settings Mode** | `Space` | Enter observation mode |
| **Observation Mode** | `W` `A` `S` `D` + `LMB` | Navigate the scene |
| **Observation Mode** | `Space` | Return to settings mode |

## Tech Stack

- **Silk.NET** — Low-level .NET bindings for OpenGL
- **GLSL** — Custom vertex and fragment shaders
- **C#** — Application logic and buffer management

---------------------------------------------------------------

### Skull model (Marmor)

![Skull](https://github.com/maybebool/GraphicsProgramming/blob/main/SAE.5300S1/Images/1.jpg)


### Spiral (Chipboard)

![Spiral](https://github.com/maybebool/GraphicsProgramming/blob/main/SAE.5300S1/Images/2.jpg)


### Diamond (Polished)

![Diamond](https://github.com/maybebool/GraphicsProgramming/blob/main/SAE.5300S1/Images/3.jpg)


### Icosahedron (Concrete)

![Icosahedron](https://github.com/maybebool/GraphicsProgramming/blob/main/SAE.5300S1/Images/4.jpg)


### Moebius Strip (Mirror effect)

![Moebius](https://github.com/maybebool/GraphicsProgramming/blob/main/SAE.5300S1/Images/5.jpg)


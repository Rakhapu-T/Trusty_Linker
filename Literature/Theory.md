---
tags:
  - notes
---
# Sliding Mode Control
Here is a MATLAB ted talk by Brian Douglas to explain the operation:
<iframe width="560" height="315" src="https://www.youtube.com/embed/RD-2oiwEbDo?si=HoarjUeKIAlFwj0c" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Watch the video to build some intuition!

### Mathematical Derivation
1. For an n dimensional state system. where $x$ is the $n \times 1$ vector of the controllable system states. Rewrite the system in the following form:
$$\dot{x} = f(x) + g(x)u(t)$$
	The above form can be used for both linear and non-linear systems.

2. Define a switching function $S = C^T x$ (Linear combination of all of the states):

3. Define S such that $S \cdot S <0$ (Readability condition):
$$\dot{S} = h(S(x))$$
$$\dot{S} = \frac{\partial S}{\partial x} \frac{\partial x}{\partial t} = C^T \cdot [\dot{x}] = C^T \cdot [f(x) + g(x)u(t)]
$$
$$\implies u(t) = (C^T g(x))^{-1}(-C^Tf(x) + h(S(x)))$$
Where the equation above is the standard sliding mode control compensator formular.
 
# Finite Differences Applied to 1D Steady State Convection Diffusion Equation
## Introduction

The Convection Diffusion Equation is a scalar transport equation, that represents the distribution of particles in an area due to diffusion, convection and sources and sinks. In general it takes the form:

$$\partial_tC=\vec\nabla\cdot(\mu\vec\nabla C-\vec vC) + R​$$

Where $C$ is the quantity of interest, $\mu$ the coefficient of diffusivity, $\vec v$ velocity field (convection), $R$ the reaction term indicating any sources or sinks. 

An intuitive example of this equation is oil contaminating a river. $C$ is the concentration of oil in a particular region. $\partial_tC$ represents the change of oil concentration over time and $\mu$ represents the propensity for the oil to diffuse in the river. $\vec v$ represents the flow of the river—which regions have fast or slow current. $R$ represents any action directly done to impact the oil concentration, such as dumping oil (source) or cleaning up the oil (sink). 

This project will look at the simple case—one dimension, there are no sources or sinks, and that the state of the system is currently at equilibrium (i.e. $\partial_t C=0$). Dealing this simple case provides a basis for graduating to more complex cases, as well dealing with the generalized transport equation. 

Under these conditions the equation now reads:

$$-\mu \frac{d^2C}{dx^2}+v\frac{dC}{dx}=0$$

Which is a classic second order, linear differential equation. This project will consider the boundary value problem of this equation:

$$-\mu \frac{d^2 y}{dx^2} + \beta \frac{dy}{dx}=0 $$

$$ y(0)=0 \quad y(1)=1 , \quad \mu>0, \quad \beta > 0 \quad x\in(0,1) \quad \mu,\beta\in\mathbb{R}$$

## Finite Differences

While analytical solution of our equation can be found, this is not the case for most differential equations. As equations and their boundary and initial conditions become more complex, there is a greater need for numerical methods. 

Finite differences is a numerical method where instead of letting the function $y$ be continuous, it is discretized. The issue with discretizing differential equations is that the derivative is a continuous operator, hence there will always be an error associated the numerical solution. The hope is that as the discretization interval becomes smaller, the numerical solution approaches the analytical solution. 

Let $u_i$ be the numerical solution and $y$ be our analytical solution

Inspired from the limit definition of the derivative, we can define our derivatives as such using a central difference: 
```math
(\partial^2_x u)_i= \frac{u_{i+1}-2u_{i}+u_{i-1}}{\Delta x^2}
```

```math
(\partial_xu)_i=\frac{u_{i+1}-u_{i-1}}{2\Delta x}
```
By substituting our definitions into the differential equation, we get its discretized form.
## Derivation of Matrix-Vector Product form

After shifting the indices to range from $n$ to $n-2$, the discretized form of our equation reads:
```math
\Bigg( \frac{-\mu}{\Delta x^2} - \frac{\beta}{2\Delta x} \Bigg) u_{n-2} + \Bigg( \frac{2\mu}{\Delta x^2} \Bigg) u_{n-1} + \Bigg( \frac{-\mu}{\Delta x^2} + \frac{\beta}{2\Delta x} \Bigg)u_n = 0
```

$$u_0= y(a) \quad u_n = y(b), \quad x\in(a,b)$$

While computationally, one can iterate over the range of the index, due to the nature of the boundary conditions, it is not ideal. Mathematically it is easier to convert this problem into a linear algebra problem. Upon inspection we can see that this is really a $n\times n$ system of equations for the range of the index. In matrix form:

![Matrix Equation](Matrix1.svg)

This form allows for easier implementation of the boundary conditions:

$$ \left[ \begin{matrix} \left( \frac{1}{\Delta x^2} \right) & 0\\

\left( \frac{-\mu}{\Delta x^2} - \frac{\beta}{2\Delta x} \right) & \left( \frac{2\mu}{\Delta x^2} \right) & \left( \frac{-\mu}{\Delta x^2} + \frac{\beta}{2\Delta x} \right) \\

& \ddots & \ddots & \ddots \\

& & \ddots & \ddots &\ddots \\

& & & \left( \frac{-\mu}{\Delta x^2} - \frac{\beta}{2\Delta x} \right) & \left( \frac{2\mu}{\Delta x^2} \right) & \left( \frac{-\mu}{\Delta x^2} + \frac{\beta}{2\Delta x} \right) \\

& & & & 0 & \left( \frac{1}{\Delta x^2} \right)

\end{matrix}\right] \left[ \begin{matrix} u_0 \\ \vdots \\ \vdots \\ u_n \end{matrix}\right] = \left[ \begin{matrix} \frac{y(a)}{\Delta x^2} \\ 0 \\ \vdots \\ 0 \\ \frac{y(b)}{\Delta x^2} \end{matrix}\right]$$


Recall that $y(a)=0$ and $y(b)=1$:

$$ \left[ \begin{matrix} \left( \frac{1}{\Delta x^2} \right) & 0\\

\left( \frac{-\mu}{\Delta x^2} - \frac{\beta}{2\Delta x} \right) & \left( \frac{2\mu}{\Delta x^2} \right) & \left( \frac{-\mu}{\Delta x^2} + \frac{\beta}{2\Delta x} \right) \\

& \ddots & \ddots & \ddots \\

& & \ddots & \ddots &\ddots \\

& & & \left( \frac{-\mu}{\Delta x^2} - \frac{\beta}{2\Delta x} \right) & \left( \frac{2\mu}{\Delta x^2} \right) & \left( \frac{-\mu}{\Delta x^2} + \frac{\beta}{2\Delta x} \right) \\

& & & & 0 & \left( \frac{1}{\Delta x^2} \right)

\end{matrix}\right] \left[ \begin{matrix} u_0 \\ \vdots \\ \vdots \\ u_n \end{matrix}\right] = \left[ \begin{matrix} 0 \\ 0 \\ \vdots \\ 0 \\ \frac{1}{\Delta x^2} \end{matrix}\right]$$
Our differential equation has been reduced to a simple Matrix vector product:

$$A(\Delta x)\vec{u}=\vec{b}(\Delta x)$$

where $A$ and $\vec{b}$ is a matrix and vector respectively, dependent on the discretization interval, and $\vec{u}$ is our array representing our numerical solution. This is easy to solve. 

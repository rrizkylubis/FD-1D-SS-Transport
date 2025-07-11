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

$$u_0= y(a) \quad u_n = y(b), \quad \mu > 0, \quad \beta > 0, \quad x\in(a,b)$$

While computationally, one can iterate over the range of the index, due to the nature of the boundary conditions, it is not ideal. Mathematically it is easier to convert this problem into a linear algebra problem. Upon inspection we can see that this is really a $n\times n$ system of equations for the range of the index. In matrix form:

![Matrix Equation](Matrix1.png)

This form allows for easier implementation of the boundary conditions:

![Matrix Equation](Matrix2.png)

Recall that $y(a)=0$ and $y(b)=1$:

![Matrix Equation](Matrix3.png)

Our differential equation has been reduced to a simple Matrix vector product:

$$A(\Delta x)\vec{u}=\vec{b}(\Delta x)$$

where $A$ and $\vec{b}$ is a matrix and vector respectively, dependent on the discretization interval, and $\vec{u}$ is our array representing our numerical solution. This is easy to solve. 

## Errors and Stability

The analytical solution to the boundary value problem is:

$$y(x)=\frac{e^{\beta x/\mu}-1}{e^{\beta/\mu}-1}$$

In [Convection_Diffusion_Solver.ipynb](Convection_Diffusion_Solver.ipynb) the numerical solution is computed and compared graphically to the analytical solution for a few values of $\beta$, $\mu$ was set to 1. Below is $\beta = 10, \quad \Delta x = 0.005$

<p align="center">
  <img src="Numerical_Solution_B10.png" width="500">
</p>

Via Taylor Expansion and Taylor's remainder theorem of each term of our numerical derivatives we can obtain the local truncation error of our central difference derivatives:


$$f'(x) = \frac{f(x+\Delta x)-f(x-\Delta x)}{2 \Delta x}-\Delta x^2 \frac{f'''(\xi_1)+f'''(\xi_2)}{12}$$

$$f''(x)=\frac{f(x+\Delta x)-2f(x)+f(x-\Delta x)}{\Delta x^2}-\Delta x^2\frac{f^{(4)}(\xi_1)+f^{(4)}(\xi_2)}{24}$$

$$\xi_1\in(x,x+\Delta x) \quad \xi_2\in(x-\Delta x,x)$$

As we can see, the first term in each of our derivatives is our numerical derivative. The second term is the remainder given via Taylor's Theorem, and it is dependent on $\Delta x^2$. This means the error of our numerical solution is $O(\Delta x^2)$, as we halve the discretization interval, our numerical approximation gets four times better. Below is an example where $\beta = 10, \quad \mu = 1$, with each discretization interval being halved. Code can be found in [CD_Error_Calculation.ipynb](CD_Error_Calculation.ipynb).

<p align="center">
  <img src="Error_Fit.png" width="500">
</p>

While the center scheme finite difference is convergent to $O(\Delta x^2)$ it is important to note that the numerical solution is conditionally stable, i.e. oscillations are present under certain conditions leading to no similarity to the analytical solution. The condition for stability is:

$$\frac{|\beta|\Delta x}{2\mu}<1$$

Below we can see two different scenarios, one not meeting the stability condition and the other meeting the condition due to the difference in discretization interval. We can see oscillations present in the one not meeting the stability condition. In practical regards, so long as the discretization interval is sufficiently large and computational power is available, the stability condition is not a matter of worry.

<p align="center">
  <img src="Ossilations.png" width="400"> <img src="No_Ossilations.png" width="400">
</p>

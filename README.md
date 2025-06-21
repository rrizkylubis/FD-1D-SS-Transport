# Finite Differences Applied to 1D Steady State Convection Diffusion Equation
## Introduction

The Convection Diffusion Equation is a scalar transport equation, that represents the distribution of particles in an area due to diffusion, convection and sources and sinks. In general it takes the form:

$$\partial_tC=\vec\nabla\cdot(D\vec\nabla C-\vec vC) + R​$$
Where $C$ is the quantity of interest, $D$ the coefficient of diffusivity, $\vec v$ velocity field (convection), $R$ the reaction term indicating any sources or sinks. 

An intuitive example of this equation is oil contaminating a river. $C$ is the concentration of oil in a particular region. $\partial_tC$ represents the change of oil concentration over time and $D$ represents the propensity for the oil to diffuse in the river. $\vec v$ represents the flow of the river—which regions have fast or slow current. $R$ represents any action directly done to impact the oil concentration, such as dumping oil (source) or cleaning up the oil (sink). 

This project will look at the simple case—there are no sources or sinks, and that the state of the system is currently at equilibrium (i.e. $\partial_t C=0$). Dealing this simple case provides a basis for graduating to more complex cases, as well dealing with the generalized transport equation. 

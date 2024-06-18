# inverted-cart-pend
Toy control problem involving an inverted pendulum on a cart, stabilized by a proportional controller designed using a pole-placement aglorithm.

# Modelling
Cart
- 1D motion along x-axis
- Frictionless
- Neglect wheels' rotational inertia

Pendulum
- Rigid
- Connected to cart by a pin

Equations of Motion
```math
\ddot{x} = \frac{ml^2 + I}{M + m - m^2 l^2 \cos^2\theta}\left[ u - c\dot{x}+ \frac{m^2l^2g\sin\theta\cos\theta}{ml^2 + I} + ml\dot{\theta}^2\sin\theta \right]
```

```math
\ddot{\theta} = \frac{M + m}{ml^2 + I - m^2l^2\cos^2\theta}\left[ mgl\sin\theta - \frac{ml\cos\theta}{M+m} \left( u - c\dot{x} + ml\dot{\theta}^2\sin\theta \right) \right]
```

State vector
```math
\mathbf{z} = \begin{bmatrix} x & \theta & \dot{x} & \dot{\theta} \end{bmatrix}^T
```

# Linearization
System is in equilibrium at 
```math
\mathbf{z}_0 = \begin{bmatrix} 0 & 0 & 0 & 0 \end{bmatrix}
```
Computing the Jacobian with respect to $\mathbf{z}$ and substituting $\mathbf{z}\leftarrow\mathbf{z}_0$,
```math
\left. \frac{d\mathbf{\dot{z}}_i}{d\mathbf{z}_j} \right|_{\mathbf{z}=\mathbf{z}_0}
=
\begin{bmatrix}
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 \\
0 & \frac{m^2l^2g}{\left(ml^2 + I\right)\left( M + m - \frac{m^2l^2}{ml^2 + I} \right)} & -\frac{c}{M + m - \frac{m^2l^2}{ml^2 + I}} & 0 \\
0 & \frac{mgl}{ml^2 + I - \frac{m^2l^2}{M + m}} & \frac{mlc}{\left(M + m\right)\left( ml^2 + I - \frac{m^2l^2}{M+m} \right)} & 0
\end{bmatrix}
```
For $u$,
```math
\left.\frac{d\mathbf{\dot{z}}_i}{du} \right|_{\mathbf{z}=\mathbf{z}_0}
=
\begin{bmatrix}
  0 \\ 0 \\ \frac{1}{ M + m - \frac{m^2l^2}{ml^2 + I} } \\ -\frac{ml}{\left(M+m\right)\left( ml^2 + I - \frac{m^2l^2}{M+m} \right)}
\end{bmatrix}
```
Using these matrices, the system can be linearized (approximated by a first-order Taylor polynomial) as follows.
```math
\frac{d\mathbf{z}}{dt} = \left[ \left. \frac{d\mathbf{\dot{z}}_i}{d\mathbf{z}_j} \right|_{\mathbf{z}=\mathbf{z}_0} \right]\mathbf{z} + \left[ \left.\frac{d\mathbf{\dot{z}}_i}{du} \right|_{\mathbf{z}=\mathbf{z}_0} \right]u
```

# Control Law
Proportional control is defined by
```math
  u = \left[K\right]\mathbf{z}
```
In this case, the gain matrix $\left[K\right]\in\mathbb{R}^{1\times4}$. Substituting into the linearized system,
```math
\frac{d\mathbf{z}}{dt} = \underbrace{\left(\left[ \left. \frac{d\mathbf{\dot{z}}_i}{d\mathbf{z}_j} \right|_{\mathbf{z}=\mathbf{z}_0} \right] + \left[ \left.\frac{d\mathbf{\dot{z}}_i}{du} \right|_{\mathbf{z}=\mathbf{z}_0} \right]\left[K\right] \right)}_{\left[A\right]} \mathbf{z}
```
This admits the solution
```math
\mathbf{z}(t) = e^{\left[A\right]t}\mathbf{z}_{t=0}
```

# Pole Placement
Without loss of generality, let $\mathbf{z}(t) = \mathbf{x}^* - \mathbf{x}(t)$ where $\mathbf{x}^*$ is a reference state and $\mathbf{x}(t)$ is the current state. Pole placement finds a matrix $\left[K\right]$ such that the eigenvalues of $\left[A\right]$ are all negative. If $\left[K\right]$ has negative eigenvalues,
```math
\lim_{t\to\infty}\mathbf{z}(t) = 0 \implies \lim_{t\to\infty}\mathbf{x}(t) = \mathbf{x}^*
```

# Results
Uncontrolled system ($\theta_0=0.1$, $\dot{x}_0=0.1$)
![unforced](https://github.com/Gunnar703/inverted-cart-pend/assets/109893121/64c0b4af-d8c1-4486-99b9-9c880afa1b11)

Controlled system (same ICs)
![forced](https://github.com/Gunnar703/inverted-cart-pend/assets/109893121/628057e0-f804-44a1-b676-15cfeaec9fba)


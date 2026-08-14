+++
title = 'Double pendulum'
date =  2026-08-14 17:44:11
[params]
  math = true
+++

A double pendulum simulation was my kind of fun when I found some time during a few days in August. It was a relatively easy project which after I finished I (and my children sometimes) find captivating to just watch for a few minutes. Maybe there is something in this chaotic dancing that stimulates some part of our brains and makes us not want to take our eyes off of it. Maybe it is only in my genes, don't know. [Here is my simulation](https://dikatrio.xyz/double).

### GPU memory

The most challenging part for me was to figure out how to use different GPU buffers to draw different parts with webgl. [Previously](https://dikatrio.xyz/blog/nbody/) I had learned how to use only one buffer where I stored all the vertices I wanted to draw. Now though I used one GPU buffer to store the vertices of the circles and a separate one for the vertices of the connecting lines. The lines are only connecting the centers of the two circles, and the circles are made of triangles.

In short, a you allocate vertices data to a bound GPU buffer and you draw the data once a vertex array is bound. With the vertex array you indicate how to pull the data out of the buffer. When the vertex array is bound you can draw the data using the compiled shaders. At least that is how I think I understand it. When I need to learn something new about webgl I always look at [webgl2fundamentals.org](https://webgl2fundamentals.org/).

So the basic draw algorithm that worked out for me is
* bind the buffer
* fill in its data store (updated coordinates of vertices)
* bind the vertex array
* draw the array

and repeat this for both the triangles data and the lines data. After that only the numerical integration remained to do to find the updated coordinates of the circles.

### Runge Kutta

The maths to derive the equations of the system are described [here](https://web.mit.edu/jorloff/www/chaosTalk/double-pendulum/double-pendulum-en.html). Essentially it comes down to a system of four equations with four variables, the angles of the masses to the perpendicular axis and their angular velocities. With the [Runge Kutta method](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods) you calculate the derivatives at four different points \\(k_1, k_2, k_3, k_4\\) of the interval. The tricky part in this problem is to calculate those for all four derivative equations, and add the value of each derivative to each corresponding variable. In my code, it looks like this
```
let derivatives = System::rk4(H, vars, f);
vars = vars + (derivatives * (H / 6.));
```

where vars are \\(\theta_1, \theta_2, \omega_1, \omega_2\\), essentially the state of the system
```
struct State {
    theta1: f32,
    theta2: f32,
    omega1: f32,
    omega2: f32,
}
```

and the derivatives are
```
struct Derivatives {
    f1: f32,
    f2: f32,
    f3: f32,
    f4: f32,
}
```

So critically, each variable is updated with its derivative value
```
impl Add<Derivatives> for State {
    type Output = State;

    fn add(self, other: Derivatives) -> State {
        Self {
            theta1: self.theta1 + other.f1,
            theta2: self.theta2 + other.f2,
            omega1: self.omega1 + other.f3,
            omega2: self.omega2 + other.f4,
        }
    }
}
```

### Correct chaos

It is very interesting how the double pendulum system is highly sensitive on the initial conditions. Its motion is chaotic and it differs a lot when the initial conditions differ only slightly. Also even though the Runge Kutta method is a fourth-order method, the error accumulates and that has a relatively big impact on the system's orbit. Maybe some [symplectic integrator](https://en.wikipedia.org/wiki/Symplectic_integrator) gives a more accurate solution.

The accumulated numerical error can be seen in the simulation if the angles are both set to 180 degrees. In this way both point masses are exactly up on the perpendicular axis and gravity should not pull them left or right. But the equations are solved for each frame of the simulation (because of rust and webgl is it fast and you do not notice it), and after about 30 seconds the pendulum drops to one side, indicating how the tiny numerical error affects greatly the chaotic behaviour of the system.

**Algebra**: It's arithmetic that includes non-numerical entities like $x$

If it has an exponential term, it isn't linear algebra...
$2x^2 + 25 = 25$
$2\sqrt{x} + 5 = 25$

**Linear algebra**: Solving for unknowns within system of linear equations.

---

Example: 
- Sheriff has $180 km/h$ car. 
- Bank robber has $150 km/h$ car and five minute head-start
- How long does it take the sheriff to catch the robber?
- What distance will they have traveled at that point?

We can solve this problem graphically...

![[Screenshot 2023-05-18 at 20.51.17.png]]

Or we can solve it algebraically 

### Equation 1
We can represent the first equation like the following since the car is going at 2.5 $km/min$

Equation 1: $d = 2.5t$

### Equation 2
We can do something similar with this equation, but here it's not growing like the equation 1 because we are 5 min ahead, that's why we do the following

Equation 2: $d = 3 (t-5)$

Then we solve the equation system.
...
$2.5t=3(t-5)$
$2.5t=3t-15$
$2.5t - 3t = -15$
$-0.5t = -15$
$t=$$-15\over-0.5$
$t=$$30(min)$

### Plotting

plotting this problem with python

```python
import numpy as np
import matplotlib.pyplot as plt

# -----------------------------------------------------

t = np.linspace(0, 40, 1000) # start, finish, n points

d_r = 2.5 * t
d_s = 3 * (t-5)

fig, ax = plt.subplots()
plt.title('A Bank Robber Caught')
plt.xlabel('time (in minutes)')
plt.ylabel('distance (in km)')
ax.set_xlim([0, 40])
ax.set_ylim([0, 100])
ax.plot(t, d_r, c='green')
ax.plot(t, d_s, c='brown')
plt.axvline(x=30, color='purple', linestyle='--')
_ = plt.axhline(y=75, color='purple', linestyle='--')
plt.show()

```

---
In a given system of equations could be many unknowns in each equation.

$y = a + b x_{1} + c x_{2} + ... + m x_{m}$
*This is what we call a regression model 

 $y$: house price
 $a$: "y-intercept" or average
 $b$: distance to school
 $c$: number of bedrooms
$$
\begin{bmatrix}
y1 | a+bx_{1,1} +cx_{1,2} + ... + mx_{1,m}\\
y2 | a+bx_{2,1} +cx_{2,2} + ... + mx_{2,m}\\
.\\
.\\
.\\
y3 | a+bx_{3,1} +cx_{3,2} + ... + mx_{3,m}\\
y4 | a+bx_{4,1} +cx_{4,2} + ... + mx_{4,m}\\
\end{bmatrix}
$$

For any house $i$ in the data set, $y_{i}=price$ and $x_{i,1}$ to $x_{i,m}$ are its features we solve for parameters $a$, $b$, $c$, to $m$


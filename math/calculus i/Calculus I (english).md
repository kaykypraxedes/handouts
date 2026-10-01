```
 _____         _               _               _____ 
/  __ \       | |             | |             |_   _|
| /  \/  __ _ | |  ___  _   _ | | _   _  ___    | |  
| |     / _` || | / __|| | | || || | | |/ __|   | |  
| \__/\| (_| || || (__ | |_| || || |_| |\__ \  _| |_ 
 \____/ \__,_||_| \___| \__,_||_| \__,_||___/ |_____|
```

# 1. Limits and Continuity

## Limit at a Point:

$\displaystyle f\left(a\right)$ indicates the value of the function $\displaystyle f\left(x\right)$ when $\displaystyle x = a$. In contrast, $\displaystyle \lim_{x \to a} f\left(x\right)$ describes the behavior of the function as $\displaystyle x$ approaches $\displaystyle a$.

![Limit of 1/|x| as x approaches zero](images/screenshot001.png)<br>
*Source: Created by the author (2025).*

For $\displaystyle f\left(x\right)=\frac{1}{\left\lvert x\right\rvert}$: $\displaystyle f\left(0\right)$ does not exist; $\displaystyle \lim_{x \to 0} f\left(x\right) = \infty$ (as $\displaystyle x$ approaches $\displaystyle 0$, $\displaystyle y$ tends to infinity).

### One-Sided Limits:

**For a limit to exist (two-sided), when approaching a point from both sides, the one-sided limits must be equal** (the same value as the function approaches from the left and right).

$$
\displaystyle \lim_{x \to a^-} f\left(x\right) = \lim_{x \to a^+} f\left(x\right) = L \Rightarrow \lim_{x \to a} f\left(x\right) = L
$$

![One-sided limits of 1/x at zero](images/screenshot002.png)<br>
*Source: Created by the author (2025).*

$$
\displaystyle \lim_{x \to 0^-} \frac{1}{x} = -\infty \quad \text{e} \quad \lim_{x \to 0^+} \frac{1}{x} = \infty \quad \Rightarrow \quad \nexists\lim_{x \to 0} \frac{1}{x}
$$

## Properties of Limits:

For finite limits, the operations below can be performed separately, respecting the indicated conditions:

| Property | Relation | Example |
|---|---|---|
| Constant | $\displaystyle \lim_{x\to a}c=c$ | $\displaystyle \lim_{x\to\infty}2=2$ |
| Identity | $\displaystyle \lim_{x\to a}x=a$ | $\displaystyle \lim_{x\to7}x=7$ |
| Multiplication by a constant | $\displaystyle \lim_{x\to a}\left[\alpha f\left(x\right)\right]=\alpha\lim_{x\to a}f\left(x\right)$ | $\displaystyle \lim_{x\to7}4x=4\cdot7=28$ |
| Sum | $\displaystyle \lim_{x\to a}\left[f\left(x\right)+g\left(x\right)\right]=\lim_{x\to a}f\left(x\right)+\lim_{x\to a}g\left(x\right)$ | $\displaystyle \lim_{x\to7}\left(x+2\right)=7+2=9$ |
| Product | $\displaystyle \lim_{x\to a}\left[f\left(x\right)g\left(x\right)\right]=\left(\lim_{x\to a}f\left(x\right)\right)\left(\lim_{x\to a}g\left(x\right)\right)$ | $\displaystyle \lim_{x\to2}\left[x^2\left(2x+1\right)\right]=4\cdot5=20$ |
| Quotient | $\displaystyle \lim_{x\to a}\frac{f\left(x\right)}{g\left(x\right)}=\frac{\lim_{x\to a}f\left(x\right)}{\lim_{x\to a}g\left(x\right)}$, if the limit of the denominator is not zero. | $\displaystyle \lim_{x\to2}\frac{x^2}{2x+1}=\frac45$ |

For a composition, if $\displaystyle g\left(x\right)\to L$ and $\displaystyle f$ is continuous at $\displaystyle L$, we can apply the outer function to the limit of the inner function:

$$
\displaystyle \lim_{x\to a}f\left(g\left(x\right)\right)=f\left(\lim_{x\to a}g\left(x\right)\right)
$$

Let $\displaystyle f\left(x\right)=x^2$ and $\displaystyle g\left(x\right)=2x+1$. Then:

$$
\displaystyle \lim_{x\to2}\left(2x+1\right)^2=\left(\lim_{x\to2}\left(2x+1\right)\right)^2=5^2=25
$$

### Continuity at a Point:

If $\displaystyle f$ is continuous at $\displaystyle a$, the value that the function tends to is the value of the function at the point itself. This allows us to calculate the limit by direct substitution:

$$
\displaystyle \lim_{x\to a}f\left(x\right)=f\left(a\right)
$$

For this equality to make sense, $\displaystyle f\left(a\right)$ must exist, as must the limit. The existence of the limit alone does not require the function to be defined at the point.

## Limits at Infinity:

When we evaluate a limit at infinity, the function may approach a real number, increase or decrease without bound, or fail to settle into a single behavior, as occurs in an oscillation:

| Behavior | Example |
|---|---|
| Convergence to a real number | $\displaystyle \lim_{x\to\infty}\left(1+\frac1x\right)=1+0=1$ |
| Tendency to $\displaystyle +\infty$ or $\displaystyle -\infty$ | $\displaystyle \lim_{x\to\infty}x^3=+\infty$ |
| Oscillation without a limit | $\displaystyle \lim_{x\to\infty}\sin x$ does not exist. |

> Writing that a limit is infinite describes an increase or decrease without bound; it does not mean that infinity is a real number reached by the function.

## Indeterminate Limits:

When substituting values or analyzing the parts of an expression separately, we may encounter an **indeterminate form**: on its own, it does not allow us to determine the value of the limit. Some cases are:

| Form | Example |
|---|---|
| $\displaystyle \frac{0}{0}$ | $\displaystyle \lim_{x\to1}\frac{x^2-1}{x-1}$ |
| $\displaystyle \frac{\infty}{\infty}$ | $\displaystyle \lim_{x\to\infty}\frac{3x^2-1}{x^2+4}$ |
| $\displaystyle \infty-\infty$ | $\displaystyle \lim_{x\to\infty}\left(x^2-x\right)$ |
| $\displaystyle 0\cdot\infty$ | $\displaystyle \lim_{x\to\infty}\left(\frac1x e^x\right)$ |
| $\displaystyle 1^\infty$ | $\displaystyle \lim_{x\to\infty}\left(1+\frac1x\right)^x$ |
| $\displaystyle \infty^0$ | $\displaystyle \lim_{x\to\infty}\left(1+x\right)^{\frac{1}{x}}$ |

To resolve these indeterminate forms, there are some tools involving both algebraic manipulation and analysis of the relationships between functions.

### Factoring Out Dominant Powers:

Particularly for polynomials tending to infinity, growth is faster in higher-degree polynomials, making them dominant:

$$
\displaystyle
\lim_{x\to\infty}\frac{3x^2-1}{x^2+4}=\lim_{x\to\infty}\frac{x^2\left(3-\frac{1}{x^2}\right)}{x^2\left(1+\frac{4}{x^2}\right)}=\lim_{x\to\infty}\frac{3-\frac{1}{x^2}}{1+\frac{4}{x^2}}=\frac31=3
$$

### Factoring:

Similar to the **factoring out dominant powers** method, but not factoring out only the variable (useful when an expression is easy to factor):

$$
\displaystyle \lim_{x \to 1} \frac{x^2 - 1}{x - 1} = \lim_{x \to 1} \frac{\left(x+1\right)\left(x-1\right)}{x - 1} = \lim_{x \to 1} \left[x + 1\right] = 2
$$

### Substitution of Variables:

With $\displaystyle h=x-1$, we have $\displaystyle h\to0$ when $\displaystyle x\to1$:

$$
\displaystyle \begin{aligned}
\lim_{x\to1}\frac{x^2-1}{x-1}=\lim_{h\to0}\frac{\left(h+1\right)^2-1}{h}=&\lim_{h\to0}\frac{h^2+2h}{h}\\=&\lim_{h\to0}\left(h+2\right)=2
\end{aligned}
$$

### Rationalization:

The idea is to multiply by a convenient expression to form a special product and simplify the indeterminate form. This manipulation is not limited to square roots.

$$
\displaystyle \begin{aligned}
\lim_{x\to0}\frac{\sqrt{x^2+9}-3}{x^2}
&=\lim_{x\to0}\frac{\left(\sqrt{x^2+9}-3\right)\left(\sqrt{x^2+9}+3\right)}{x^2\left(\sqrt{x^2+9}+3\right)}\\
&=\lim_{x\to0}\frac{x^2+9-9}{x^2\left(\sqrt{x^2+9}+3\right)}\\
&=\lim_{x\to0}\frac1{\sqrt{x^2+9}+3}\\
&=\frac1{\sqrt9+3}=\frac16
\end{aligned}
$$

### Squeeze Theorem (Sandwich):

Even if we do not yet know how to calculate the limit of $\displaystyle f\left(x\right)$, we can compare it with two other functions. If $\displaystyle g\left(x\right)\le f\left(x\right)\le h\left(x\right)$ for points sufficiently close to $\displaystyle a$, except possibly $\displaystyle a$ itself, and **the two outer functions tend to the same value**, the intermediate function also tends to that value:

$$
\displaystyle \lim_{x\to a}g\left(x\right)=\lim_{x\to a}h\left(x\right)=L
\quad\Rightarrow\quad
\lim_{x\to a}f\left(x\right)=L
$$

To calculate $\displaystyle \lim_{x\to0}x^2\sin\left(\frac{1}{x}\right)$, even though $\displaystyle \lim_{x\to0}\sin\left(\frac{1}{x}\right)$ does not exist, we have:

$$
\displaystyle -1\le\sin\left(\frac1x\right)\le1
\quad\Rightarrow\quad
-x^2\le x^2\sin\left(\frac1x\right)\le x^2
$$

Since the limits of $\displaystyle -x^2$ and $\displaystyle x^2$ are zero, the function is “squeezed” between values approaching zero. Therefore:

$$
\displaystyle \lim_{x\to0}x^2\sin\left(\frac1x\right)=0
$$

![Squeeze theorem for x^2 sin(1/x)](images/screenshot003.png)<br>
*Source: Created by the author (2025).*

## Intermediate Value Theorem (IVT):

If $\displaystyle f$ is a continuous function on an interval $\displaystyle \left[a, b\right]$, and $\displaystyle d$ is between $\displaystyle f\left(a\right)$ and $\displaystyle f\left(b\right)$, then there is a value $\displaystyle c$ such that $\displaystyle f\left(c\right) = d$.

$$
\displaystyle \min\left\{f\left(a\right),f\left(b\right)\right\}\le d\le\max\left\{f\left(a\right),f\left(b\right)\right\}
\quad\Rightarrow\quad
\exists c\in\left[a,b\right]:\ f\left(c\right)=d
$$

## Formalizing the Concept of a Limit:

$$
\displaystyle \forall \varepsilon > 0\ \exists \delta > 0 \text{ tal que, }\forall x \in D_f \text{: }
0 < \left|x-a\right| < \delta \Rightarrow \left|f\left(x\right) - L\right| < \varepsilon
$$

**The value of $\displaystyle f\left(x\right)$ can be made arbitrarily close to $\displaystyle L$ by bringing $\displaystyle x$ close to $\displaystyle a$, without requiring $\displaystyle x=a$.** For each margin of error $\displaystyle \varepsilon>0$ chosen in $\displaystyle y$, there is a distance $\displaystyle \delta>0$ in $\displaystyle x$ that guarantees this closeness.

![Relationship between epsilon, delta, and closeness to the limit](images/screenshot004.png)<br>
*Source: Created by the author (2025).*

> The distances $\displaystyle \delta$ do not necessarily have to be the same on the left and right sides of $\displaystyle a$ (usually they will not be).

---

# 2. Derivatives

## Derivative at a Point:

The rate of growth of any function between 2 points can be obtained by $\displaystyle \frac{\Delta y}{\Delta x} =  \frac{y_2 - y_1}{x_2 - x_1}$, which is the slope of a secant line to the graph of $\displaystyle f$ at the points $\displaystyle \left(x_1, f\left(x_1\right)\right)$, $\displaystyle \left(x_2, f\left(x_2\right)\right)$.

Keeping one point fixed and bringing the other closer to it, when the ratio has a finite limit, we find the slope of a tangent line to the graph of $\displaystyle f$ at the point $\displaystyle \left(a, f\left(a\right)\right)$, which is the derivative (instantaneous rate of change).

$$
\displaystyle \left.\frac{dy}{dx}\right|_{x=a}
=\left.\frac{d}{dx}f\left(x\right)\right|_{x=a}
=f'\left(a\right)
=\lim_{x\to a}\frac{f\left(x\right)-f\left(a\right)}{x-a}
$$

If $\displaystyle h=x-a$, then $\displaystyle h\to0$ when $\displaystyle x\to a$, and the same definition becomes:

$$
\displaystyle f'\left(a\right)=\lim_{h\to0}\frac{f\left(a+h\right)-f\left(a\right)}h
$$

For $\displaystyle f\left(x\right) = x^3$, the rate of change between $\displaystyle x_1 = 1$, $\displaystyle x_2 = 3$, and the derivative at $\displaystyle x = 2$ are:

$$
\displaystyle \frac{\Delta y}{\Delta x} = \frac{27-1}{3-1}=\frac{26}{2}=13
$$

$$
\displaystyle \frac{dy}{dx}\Big|_{x = 2} = \lim_{x\to2}\frac{x^3-2^3}{x-2}=\lim_{x\to2}\frac{\left(x-2\right)\left(x^2+2x+4\right)}{x-2}=\lim_{x\to2}\left[x^2+2x+4\right]=12
$$

![Secant and tangent lines to the graph of x³](images/screenshot005.png)<br>
*Source: Created by the author (2025).*

### Differentiability:

A function $\displaystyle f$, differentiable at an interior point $\displaystyle a$ of its domain, has the following characteristics:

- **Value at the point:** $\displaystyle f\left(a\right)$ exists.
- **Continuity at the point:** $\displaystyle \lim_{x\to a}f\left(x\right)=f\left(a\right)$.
- **One-sided derivatives:** $\displaystyle f'_-\left(a\right)$ and $\displaystyle f'_+\left(a\right)$ exist, are finite, and are equal.

> The idea of having no “corner” helps visualize the third condition. However, continuity and the absence of a corner alone do not guarantee a finite derivative: a vertical tangent also requires care.

### Derivative as a Function:

Instead of considering the derivative of a function at a specific point, we consider an arbitrary point.

For $\displaystyle f\left(x\right)=x^2$:

$$
\displaystyle f'\left(a\right)=\lim_{x\to a}\frac{x^2-a^2}{x-a}=\lim_{x\to a}\frac{\left(x+a\right)\left(x-a\right)}{x-a}=\lim_{x\to a}\left(x+a\right)=2a
$$

Since $\displaystyle a$ is an arbitrary point, we write $\displaystyle f'\left(x\right)=2x$, for every $\displaystyle x\in\mathbb R$.

## Linear Approximation:

$$
\displaystyle f'\left(x\right)=\lim_{h\to0}\frac{f\left(x+h\right)-f\left(x\right)}h
$$

For small, nonzero $\displaystyle h$:

$$
\displaystyle f'\left(x\right)\approx\frac{f\left(x+h\right)-f\left(x\right)}h
\quad\Rightarrow\quad
f\left(x+h\right)\approx f\left(x\right)+hf'\left(x\right)
$$

For small values of $\displaystyle h$, the change in the function is practically equal to the corresponding value on the tangent line.

![Linear approximation of sin(x) at x = 1](images/screenshot006.png)<br>
*Source: Created by the author (2025).*

The difference between the values is approximately $\displaystyle 0{,}04$. Near the point of tangency, the approximation follows the function; in this example, reducing $\displaystyle h$ improves accuracy.

## Properties of Derivatives:

Operations involving derivatives that can be generalized. The functions involved must be differentiable at the points considered, and $\displaystyle \alpha$ is constant. **The derivations are in the appendix**.

### Sum of Derivatives:
$$
\displaystyle \frac{d}{dx}\left[f\left(x\right)+g\left(x\right)\right]=f'\left(x\right)+g'\left(x\right)
$$

### Derivative of a Scalar Multiple:

$$
\displaystyle \frac{d}{dx}\left[\alpha f\left(x\right)\right]=\alpha f'\left(x\right)
$$

### Product Rule:

$$
\displaystyle \frac{d}{dx}\left[f\left(x\right)g\left(x\right)\right]=f'\left(x\right)g\left(x\right)+f\left(x\right)g'\left(x\right)
$$

### Quotient Rule:

For $\displaystyle g\left(x\right)\ne0$:

$$
\displaystyle \frac{d}{dx}\left[\frac{f\left(x\right)}{g\left(x\right)}\right]=\frac{f'\left(x\right)g\left(x\right)-f\left(x\right)g'\left(x\right)}{g^2\left(x\right)}
$$

### Chain Rule:

$$
\displaystyle \frac{d}{dx} \left[f\left(g\left(x\right)\right)\right]=f'\left(g\left(x\right)\right)g'\left(x\right)
$$

## Derivative of Inverse Functions:

Considering inverse functions, on their respective domains, with $\displaystyle f\left(g\left(x\right)\right) = g\left(f\left(x\right)\right) = x$, such as $\displaystyle x^3$ and $\displaystyle \sqrt[3]{x}$, $\displaystyle e^x$ and $\displaystyle \ln{x}$, $\displaystyle \sin x$ and $\displaystyle \arcsin x$ (restricting sine to $\displaystyle \left[-\frac{\pi}{2},\frac{\pi}{2}\right]$), etc. The formula below holds at points where the function has a differentiable inverse; in particular, it requires $\displaystyle f'\left(f^{-1}\left(x\right)\right)\ne0$.

$$
\displaystyle f\left(f^{-1}\left(x\right)\right)=x
\quad\Rightarrow\quad
f'\left(f^{-1}\left(x\right)\right)\left(f^{-1}\right)'\left(x\right)=1
$$

Thus:

$$
\displaystyle \left(f^{-1}\right)'\left(x\right)=\frac1{f'\left(f^{-1}\left(x\right)\right)}
$$

Considering $\displaystyle f^{-1}\left(x\right) = \ln x \Rightarrow f\left(x\right) = e^x$, with $\displaystyle f'\left(x\right) = e^x$: $\displaystyle \dfrac{d}{dx} \ln x = \dfrac{1}{e^{\ln{x}}} = \frac{1}{x}$.

## Important Derivatives:

Some of the most common and important derivatives, serving as a basis for calculating derivatives of more complex functions. **The derivations are in the appendix**.

| Function | Derivative | Conditions |
|---|---|---|
| $\displaystyle c$ | $\displaystyle 0$ | $\displaystyle c$ is a real constant. |
| $\displaystyle \sin x$ | $\displaystyle \cos x$ | Angle in radians. |
| $\displaystyle \cos x$ | $\displaystyle -\sin x$ | Angle in radians. |
| $\displaystyle a^x$ | $\displaystyle a^x\ln a$ | $\displaystyle a>0$. |
| $\displaystyle e^x$ | $\displaystyle e^x$ | $\displaystyle x\in\mathbb R$. |
| $\displaystyle \log_a x$ | $\displaystyle \frac{1}{x\ln a}$ | $\displaystyle x>0$, $\displaystyle a>0$ and $\displaystyle a\ne1$. |
| $\displaystyle \ln x$ | $\displaystyle \frac{1}{x}$ | $\displaystyle x>0$. |

### Power Rule:

$$
\displaystyle \frac{d}{dx}x^n=nx^{n-1}
$$

The exponent “comes down” as a multiplier and decreases by one. For real exponents, the rule holds for $\displaystyle x>0$; at other points, the domain and differentiability of the power must be respected. For positive integer exponents, it holds on the entire real line.

> The constant case $\displaystyle x^0=1$ has derivative zero.

## Higher-Order Derivative:

This is simply the process of taking the derivative of a function that has already been differentiated.

$$
\displaystyle \frac{d}{dx} \left( \frac{d}{dx} f\left(x\right) \right) = \frac{d^2}{dx^2}f\left(x\right) = f''\left(x\right) \text{ ou } f^{\left(2\right)}\left(x\right)
$$

$$
\displaystyle \frac{d}{dx} \left( \frac{d}{dx} \left( \cdots \left( \frac{d}{dx} f\left(x\right) \right) \right) \right) = \frac{d^n }{dx^n}f\left(x\right) = f^{\left(n\right)}\left(x\right)
$$

$$
\displaystyle
\frac{d^3}{dx^3}\sin x=\frac{d}{dx}\left[\frac{d}{dx}\left(\frac{d}{dx}\sin x\right)\right]=\frac{d}{dx}\left(\frac{d}{dx}\cos x\right)=\frac{d}{dx}\left(-\sin x\right)=-\cos x
$$

## Derivative of an Implicit Function:

In an explicit function, the dependent variable $\displaystyle y$ is expressed directly in terms of the independent variable $\displaystyle x$, $\displaystyle y = f\left(x\right)$, whereas in an implicit relation it is not necessary or not easy to isolate $\displaystyle y$ explicitly as a function of $\displaystyle x$, and we have the relation $\displaystyle F\left(x,y\right) = 0$.

The process of finding the rate of change of one variable with respect to the other basically consists of differentiating everything and algebraically manipulating the part involving the variable of interest until we find the result.

With $\displaystyle F\left(x,y\right) = x^2 + y^2 - 25 = 0$, to find $\displaystyle \dfrac{dy}{dx}$:

$$
\displaystyle \frac{d}{dx}\left(x^2+y^2-25\right)=\frac{d}{dx}0
$$

$$
\displaystyle \frac{d}{dx}x^2+\frac{d}{dx}y^2-\frac{d}{dx}25=0
\quad\Rightarrow\quad
2x+\frac{d}{dx}y^2=0
$$

Since $\displaystyle y$ depends on $\displaystyle x$, we apply the chain rule: $\displaystyle \frac{d}{dx}\left[y\left(x\right)^2\right]=2y\frac{dy}{dx}$. Thus:

$$
\displaystyle 2x + 2y \dfrac{dy}{dx} = 0 \Rightarrow 2y \dfrac{dy}{dx} = -2x \Rightarrow \dfrac{dy}{dx} = -\dfrac{2x}{2y} = -\dfrac{x}{y},\quad y\ne0
$$

![Horizontal and vertical tangents to the circle x² + y² = 25](images/screenshot007.png)<br>
*Source: Created by the author (2025).*

At $\displaystyle \left(0,5\right)$, the slope is zero. At $\displaystyle \left(5,0\right)$, the tangent is vertical and $\displaystyle \frac{dy}{dx}$ does not exist as a finite real number.

 > Remember that $\displaystyle \frac{dy}{dx}$ does not exist as a finite real number at the point $\displaystyle \left(5,0\right)$ (the derivative at $x = 0^{+}$ is a visual simplification.
 
## Logarithmic Differentiation:

Given a very complex function, such as $\displaystyle f\left(x\right) = y = \frac{x^3 \sqrt{x^2 + 1}}{\left(3x + 2\right)^5}$, finding $\displaystyle f'\left(x\right)$ would require the product rule, quotient rule, and chain rule, making it an extremely lengthy process.

An interesting approach can be to work with logarithms. In this derivation, we consider $\displaystyle x>0$, so that all the logarithms written are defined. By the chain rule:

$$
\displaystyle \dfrac{d}{dx} \ln y = \frac{dy}{dx} \dfrac{d}{dy} \ln y = \frac{dy}{dx} \dfrac{1}{y} \Rightarrow \frac{dy}{dx} = y\,\frac{d}{dx}\ln y
$$

$$
\displaystyle \begin{aligned}
\ln y =\ln\left(\frac{x^3\sqrt{x^2+1}}{\left(3x+2\right)^5}\right)
&=\ln\left(x^3\right)+\ln\sqrt{x^2+1}-\ln\left(\left(3x+2\right)^5\right)\\
&=3\ln x+\frac12\ln\left(x^2+1\right)-5\ln\left(3x+2\right)
\end{aligned}
$$

$$
\displaystyle \begin{aligned}
\frac{d}{dx} \ln y
&=\frac{d}{dx} \left[ 3\ln x + \frac{1}{2} \ln\left(x^2 + 1\right) - 5 \ln\left(3x + 2\right) \right]\\
&= \frac{3}{x} + \frac{x}{x^2 + 1} - \frac{15}{3x+2}
\end{aligned}
$$

Since $\displaystyle \dfrac{dy}{dx} = y \dfrac{d}{dx} \ln y$:

$$
\displaystyle \frac{d}{dx} \left[ \dfrac{x^3 \sqrt{x^2 + 1}}{\left(3x + 2\right)^5} \right] = \dfrac{x^3 \sqrt{x^2 + 1}}{\left(3x + 2\right)^5}\left( \frac{3}{x} + \frac{x}{x^2 + 1} - \frac{15}{3x+2}\right)
$$

## Differentials:

Recalling the linear approximation:

$$
\displaystyle f\left(x+h\right) - f\left(x\right) \approx h f'\left(x\right)
$$

$\displaystyle h$ can be replaced by $\displaystyle \Delta x$, giving:

$$
\displaystyle \begin{aligned}f\left(x+\Delta x\right) - f\left(x\right) \approx \Delta x\,f'\left(x\right) \quad &\Rightarrow 
\quad \Delta y \approx \frac{dy}{dx} \, \Delta x \\
&\Rightarrow \quad \frac{\Delta y}{\Delta x} \approx \frac{dy}{dx}
\end{aligned}
$$

We choose $\displaystyle dx=\Delta x$ and define $\displaystyle dy=f'\left(x\right)\,dx$. Thus, $\displaystyle dy$ is the change along the tangent line, while $\displaystyle \Delta y$ is the actual change in the function. For small increments, $\displaystyle \Delta y\approx dy$.

This makes sense, since $\displaystyle \frac{\Delta y}{\Delta x} \approx \frac{dy}{dx}$ for $\displaystyle \Delta x$ close to 0.

Therefore, to find a value of the function, instead of following the path along $\displaystyle f$, we follow the tangent line at a known point ($\displaystyle y + \Delta y \approx y + dy$).

![Difference between the actual change Δy and the differential dy](images/screenshot008.png)<br>
*Source: Created by the author (2025).*

This way, it is possible to approximate functions that would be complicated to calculate.

Given $\displaystyle f\left(x\right) = \sqrt{x} \Rightarrow f'\left(x\right) = \dfrac{1}{2\sqrt{x}}$, we have, for example, $\displaystyle f\left(4\right) = \sqrt{4} =2$. $\displaystyle f\left(4{,}02\right)=2{,}004993\dots$, which is difficult to calculate manually, whereas, using differentials, $\displaystyle f\left(4{,}02\right) \approx \sqrt{4} + \dfrac{0{,}02}{2\sqrt{4}} = 2 + \frac{0{,}02}{4} = 2+0{,}005 = 2{,}005$ (an excellent approximation, since the error is less than $\displaystyle 0{,}000006$).

## L’Hôpital’s Rule:

This is a tool that allows us to resolve indeterminate limits of the form $\displaystyle \frac{0}{0}$ or $\displaystyle \frac{\infty}{\infty}$ easily. We differentiate the numerator and denominator separately:

$$
\displaystyle \lim_{x\to a}\frac{f\left(x\right)}{g\left(x\right)}=\lim_{x\to a}\frac{f'\left(x\right)}{g'\left(x\right)}
$$

To apply the rule, $\displaystyle f$ and $\displaystyle g$ must be differentiable near the point considered, except possibly at the point itself, with $\displaystyle g'\left(x\right)\ne0$ in that region. The limit of the ratio of the derivatives must exist, either finite or infinite. The rule can also be applied to one-sided limits and limits at infinity, under the corresponding conditions.

### Case of $\displaystyle 0/0$:
We can use the intuition provided by differentials: near a point where both functions vanish, their changes are approximated by their respective tangent lines. Comparing these changes suggests comparing the derivatives. This is an intuitive motivation; the validity of the rule depends on the conditions above.

$$
\displaystyle \lim_{x\to0}\frac{\sin x}{x}
=\lim_{x\to0}\frac{\cos x}{1}=1
$$

The example starts with the form $\displaystyle \frac{0}{0}$. The derivation of the derivative of sine, in the appendix, obtains this limit through geometry, without relying on L’Hôpital.

### Case of $\displaystyle \infty/\infty$:
It is quite intuitive to compare the growth of two functions. When one becomes increasingly larger in proportion to the other, the ratio may tend to infinity; the inverse ratio, to zero.

For $\displaystyle \lim_{x\to\infty}\frac{\ln\left(x\right)}{x}$, a simple analysis shows:

$$
\displaystyle \frac{\ln10}{10}\approx0{,}230,\qquad
\frac{\ln100}{100}\approx0{,}046,
$$

$$
\displaystyle \frac{\ln1000}{1000}\approx0{,}007,\qquad
\frac{\ln1000000}{1000000}\approx1{,}38\cdot10^{-5}
$$

$\displaystyle x$ grows much faster than $\displaystyle \ln x$ as $\displaystyle x\to\infty$. Even though both tend to infinity, the ratio $\displaystyle \frac{\ln\left(x\right)}{x}$ tends to zero:

$$
\displaystyle \lim_{x\to\infty}\frac{\ln x}{x}
=\lim_{x\to\infty}\frac{\frac{1}{x}}{1}=0
$$

**Other indeterminate forms.** Through algebraic manipulation, it is possible to transform other forms into a quotient suitable for the rule. In the example below, we combine the fractions and obtain $\displaystyle \frac{0}{0}$:

$$
\displaystyle \lim_{x\to0}\left(\frac1x-\frac1{\sin x}\right)
=\lim_{x\to0}\frac{\sin x-x}{x\sin x}
$$

The first application of L’Hôpital still produces $\displaystyle \frac{0}{0}$. Applying the rule again:

$$
\displaystyle \begin{aligned}
\lim_{x\to0}\frac{\sin x-x}{x\sin x}
&=\lim_{x\to0}\frac{\cos x-1}{\sin x+x\cos x}\\
&=\lim_{x\to0}\frac{-\sin x}{2\cos x-x\sin x}=0
\end{aligned}
$$

## Curve Analysis:
### Increasing and Decreasing:

The first derivative indicates the instantaneous rate of change. At a point $\displaystyle a$, if $\displaystyle f'\left(a\right)<0$, this rate is negative; if $\displaystyle f'\left(a\right)=0$, it is zero; and if $\displaystyle f'\left(a\right)>0$, it is positive.

To determine the behavior on an interval, we analyze the sign of the derivative on that interval: $\displaystyle f'\left(x\right)>0$ indicates that the function is **increasing**, while $\displaystyle f'\left(x\right)<0$ indicates that it is **decreasing**. Having a zero derivative at a point does not mean that the function is constant around it.

### Concavity and Inflection:

The second derivative allows us to analyze concavity. On an interval, if $\displaystyle f''\left(x\right)>0$, the function is **convex**, or concave up; if $\displaystyle f''\left(x\right)<0$, it is concave down.

An **inflection point** is a point where concavity changes. Finding $\displaystyle f''\left(a\right)=0$ is not enough to conclude that there is an inflection: for $\displaystyle f\left(x\right)=x^4$, we have $\displaystyle f''\left(0\right)=12\cdot0^2=0$, but concavity remains upward on both sides. An affine function also has a zero second derivative, without a change in concavity.

### Maxima and Minima:

A **local maximum** is a function value greater than or equal to nearby values; a **local minimum** is less than or equal to nearby values. If the extremum occurs at an interior point $\displaystyle a$ where the function is differentiable, then $\displaystyle f'\left(a\right)=0$.

When $\displaystyle f'\left(a\right)=0$ and the function is twice differentiable near $\displaystyle a$, we can use the second derivative test:

| Result | Conclusion |
|---|---|
| $\displaystyle f''\left(a\right)<0$ | Local maximum. |
| $\displaystyle f''\left(a\right)>0$ | Local minimum. |
| $\displaystyle f''\left(a\right)=0$ | Inconclusive test. |

The test is not a necessary condition for an extremum to exist. The function $\displaystyle x^4$, for example, has a minimum at zero, although its second derivative is zero at that point. Extrema may also exist at points where the derivative does not exist.

To be a **global maximum or minimum**, the value must be the greatest or smallest over the entire domain considered. If the function is continuous on a closed interval $\displaystyle \left[a,b\right]$, these extrema exist. To find them, we compare the values at interior candidates — where the derivative is zero or does not exist — and at the endpoints $\displaystyle f\left(a\right)$ and $\displaystyle f\left(b\right)$.

![Maxima, minima, and inflection point](images/screenshot009.png)<br>
*Source: Created by the author (2025).*

## Asymptotes:

This refers to the behavior of some functions that approach a straight line for certain values.

### Horizontal:

The line $\displaystyle y=c$ is a horizontal asymptote when $\displaystyle \lim_{x\to+\infty}f\left(x\right)=c$ or $\displaystyle \lim_{x\to-\infty}f\left(x\right)=c$, with $\displaystyle c\in\mathbb R$. Each direction is analyzed separately.

$\displaystyle \lim_{x \to -\infty} 2+\frac{1}{x} = \lim_{x \to \infty} 2+\frac{1}{x} = 2$, so $\displaystyle f$ has a horizontal asymptote at $\displaystyle y=2$.

![Horizontal asymptote of 2 + 1/x](images/screenshot010.png)<br>
*Source: Created by the author (2025).*

### Vertical:

The line $\displaystyle x=c$ is a vertical asymptote when at least one of the one-sided limits of $\displaystyle f\left(x\right)$ at $\displaystyle c$ is $\displaystyle +\infty$ or $\displaystyle -\infty$.

$\displaystyle \lim_{x \to \left(\frac{\pi}{2}\right)^-} \tan x = \infty$ and $\displaystyle \lim_{x \to \left(\frac{\pi}{2}\right)^+} \tan x = -\infty$, so $\displaystyle f$ has a vertical asymptote at $\displaystyle x = \frac{\pi}{2}$.

![Vertical asymptote of tangent at π/2](images/screenshot011.png)<br>
*Source: Created by the author (2025).*

### Oblique:

When a function $\displaystyle f$ approaches a line $\displaystyle g\left(x\right)=ax+b$, with $\displaystyle a\ne0$, such that $\displaystyle \lim_{x\to\infty}\left[f\left(x\right)-g\left(x\right)\right]=0$, that line is an oblique asymptote. The same analysis can be performed as $\displaystyle x\to-\infty$.

To find it, we can write $\displaystyle f\left(x\right)=m\left(x\right)+h\left(x\right)$, with $\displaystyle m\left(x\right)$ affine, and check whether the remainder $\displaystyle h\left(x\right)$ tends to zero. Thus, $\displaystyle f\left(x\right)\approx m\left(x\right)$ for sufficiently large values of $\displaystyle x$.

Given $\displaystyle f\left(x\right) = \frac{2x^2-3x-\ln{x}}{x+1}$, for very large values of $\displaystyle x$:

$$
\displaystyle \frac{2x^2-3x-\ln{x}}{x+1} =
\frac{2x^2-3x}{x+1} - \frac{\ln{x}}{x+1} \approx \frac{2x^2-3x}{x+1}=2x-5+\frac{5}{x+1}\approx2x-5
$$

Since $\displaystyle \lim_{x\to \infty}\left[\frac{2x^2-3x-\ln{x}}{x+1}-\left(2x-5\right)\right] = 0$, $\displaystyle 2x-5$ is an oblique asymptote of $\displaystyle f$.

![Oblique asymptote y = 2x − 5](images/screenshot012.png)<br>
*Source: Created by the author (2025).*

---

# 3. Integrals

## Antiderivatives:

Finding antiderivatives is the reverse process of differentiating: $\displaystyle F$ is an antiderivative of $\displaystyle f$ on an interval if:

$$
\displaystyle F'\left(x\right)=f\left(x\right)\quad\Longleftrightarrow\quad\int f\left(x\right)\,dx=F\left(x\right)+c
$$

The constant $\displaystyle c$ represents the constant term that is lost when differentiating.

$$
\displaystyle \frac{d}{dx}\left(2x+4\right)=2
\quad\Longleftrightarrow\quad
\int2\,dx=2x+c
$$

To recover the same function, simply take $\displaystyle c=4$, obtaining $\displaystyle 2x+4$. Every continuous function on an interval has an antiderivative on that interval.

### Common Antiderivatives and Properties:

Reversing the process of calculating the derivative:

| Integral | Antiderivative | Conditions |
|---|---|---|
| $\displaystyle \int a\,dx$ | $\displaystyle ax+c$ | $\displaystyle a$ is constant. |
| $\displaystyle \int\sin x\,dx$ | $\displaystyle -\cos x+c$ | Angle in radians. |
| $\displaystyle \int\cos x\,dx$ | $\displaystyle \sin x+c$ | Angle in radians. |
| $\displaystyle \int a^x\,dx$ | $\displaystyle \frac{a^x}{\ln a}+c$ | $\displaystyle a>0$ and $\displaystyle a\ne1$. |
| $\displaystyle \int e^x\,dx$ | $\displaystyle e^x+c$ | $\displaystyle x\in\mathbb R$. |
| $\displaystyle \int\frac{dx}{x}$ | $\displaystyle \ln\left\lvert x\right\rvert+c$ | On intervals that do not contain zero. |
| $\displaystyle \int\frac{dx}{x\ln a}$ | $\displaystyle \frac{\ln\left\lvert x\right\rvert}{\ln a}+c=\log_a\left\lvert x\right\rvert+c$ | $\displaystyle x\ne0$, $\displaystyle a>0$ and $\displaystyle a\ne1$. |
| $\displaystyle \int x^n\,dx$ | $\displaystyle \frac{x^{n+1}}{n+1}+c$ | $\displaystyle n\ne-1$, on intervals where the power and the formula are defined. |

### Sum of Integrals:

$$
\displaystyle \int\left[f\left(x\right)+g\left(x\right)\right]\,dx=\int f\left(x\right)\,dx+\int g\left(x\right)\,dx
$$

### Integral of a Scalar Multiple:
$$
\displaystyle \int \alpha f\left(x\right)\,dx=\alpha\int f\left(x\right)\,dx
$$

The constants of the antiderivatives are combined into a final constant $\displaystyle c$. In the case $\displaystyle \alpha=0$, the integral is directly $\displaystyle \int0\,dx=c$.

We cannot integrate products, quotients, or compositions simply by integrating each part separately (**there is no universal product rule or chain rule for an arbitrary integral**).

For more complex integrals, specific and more targeted methods are needed, making use of the chain and product rules of differentiation.

## Definite Integrals:

Integrals are tools that allow us to calculate the **signed area** between the graph and the $\displaystyle x$-axis on the interval $\displaystyle a\le x\le b$: above the axis, contributions are positive; below it, they are negative.

This logic comes from approximation through a sum of rectangles of width $\displaystyle \Delta x=\frac{b-a}{n}$ and height $\displaystyle f\left(x_i\right)$, where $\displaystyle x_i$ is a point chosen in the respective subinterval:

$$
\displaystyle A\approx\sum_{i=1}^n f\left(x_i\right)\Delta x
$$

For a continuous function, as the number of rectangles increases, their widths decrease and the sum approaches the integral:

$$
\displaystyle A=\lim_{n\to\infty}\sum_{i=1}^n f\left(x_i\right)\Delta x=\int_a^b f\left(x\right)\,dx
$$

> The total geometric area does not include negative contributions. Therefore, when the graph crosses the axis, the integral may differ from this total area.

![Approximation of the definite integral by rectangles](images/screenshot013.png)<br>
*Source: Created by the author (2025).*

### Fundamental Theorem of Calculus:

This relates definite integrals and antiderivatives. If $\displaystyle f$ is continuous on $\displaystyle \left[a,b\right]$ and $\displaystyle F$ is an antiderivative of $\displaystyle f$, then:

$$
\displaystyle \int_a^b f\left(x\right)\,dx = F\left(b\right) - F\left(a\right)
$$

**The derivation is in the appendix**.

### Improper Integrals:

These are integrals where the interval is infinite or the function becomes unbounded near some point of the interval. In these cases, the calculation is defined through limits.

**Infinite interval.** If only one endpoint is infinite:

$$
\displaystyle \int_a^{\infty}f\left(x\right)\,dx
=\lim_{b\to\infty}\int_a^b f\left(x\right)\,dx
=\lim_{b\to\infty}\left[F\left(b\right)-F\left(a\right)\right]
$$

$$
\displaystyle \int_{-\infty}^b f\left(x\right)\,dx
=\lim_{a\to-\infty}\int_a^b f\left(x\right)\,dx
=\lim_{a\to-\infty}\left[F\left(b\right)-F\left(a\right)\right]
$$

To integrate from $\displaystyle -\infty$ to $\displaystyle +\infty$, we choose a finite point $\displaystyle c$ and split:

$$
\displaystyle \int_{-\infty}^{\infty}f\left(x\right)\,dx
=\int_{-\infty}^c f\left(x\right)\,dx+\int_c^{\infty}f\left(x\right)\,dx
$$

**Both integrals must converge separately to finite values.** One infinite part cannot be offset by the other.

**Unbounded function.** If the problem is at an interior point $\displaystyle c\in\left(a,b\right)$, we separate the two sides:

$$
\displaystyle \int_a^b f\left(x\right)\,dx
=\lim_{u\to c^-}\int_a^u f\left(x\right)\,dx
+\lim_{v\to c^+}\int_v^b f\left(x\right)\,dx
$$

Each limit must exist and be finite. If the problem is only at one endpoint, we use only the corresponding limit, approaching from within the interval.

> Not every discontinuity makes an integral improper. A finite jump or an isolated point with no defined value does not, by itself, mean that the function grows without bound.

## Methods of Integration:

### Substitution of Variables:

Substitution uses the chain rule in reverse:

$$
\displaystyle \frac{d}{dx}f\left(g\left(x\right)\right)=f'\left(g\left(x\right)\right)g'\left(x\right)
\quad\Rightarrow\quad
\int f'\left(g\left(x\right)\right)g'\left(x\right)\,dx=f\left(g\left(x\right)\right)+c
$$

To evaluate $\displaystyle \int x\sqrt{x^2+1}\,dx$, we set $\displaystyle u=x^2+1$ and $\displaystyle du=2x\,dx$. Thus, $\displaystyle x\,dx=\frac{du}{2}$:

$$
\displaystyle \begin{aligned}
\int x\sqrt{x^2+1}\,dx=\frac12\int u^{\frac{1}{2}}\,du
&=\frac12\frac{u^{\frac{3}{2}}}{\frac{3}{2}}+c\\
&=\frac{\left(x^2+1\right)^{\frac{3}{2}}}3+c
\end{aligned}
$$

### Integration by Parts:

$$
\displaystyle \begin{aligned}
\frac{d}{dx} \left[f\left(x\right)\,g\left(x\right)\right] &= f'\left(x\right)\,g\left(x\right) + f\left(x\right)\,g'\left(x\right) \\
&\Rightarrow
\int\frac{d}{dx} \left[f\left(x\right)\,g\left(x\right)\right] \, dx= \int f'\left(x\right)\,g\left(x\right)\, dx + \int f\left(x\right)\,g'\left(x\right) \, dx\\
& \Rightarrow f\left(x\right)\,g\left(x\right) = \int f'\left(x\right)\,g\left(x\right)\, dx + \int f\left(x\right)\,g'\left(x\right) \, dx
\end{aligned}
$$

Thus:

$$
\displaystyle \int f\left(x\right)\,g'\left(x\right) \, dx=f\left(x\right)\,g\left(x\right) - \int f'\left(x\right)\,g\left(x\right)\, dx
$$

In simplified form, considering $\displaystyle f\left(x\right) = u$, $\displaystyle f'\left(x\right)\,dx = du$, $\displaystyle g\left(x\right) = v$ and $\displaystyle g'\left(x\right)\,dx = dv$:

$$
\displaystyle \int u\,dv =u\,v -\int v\,du
$$

If $\displaystyle u=x^3$, $\displaystyle du=3x^2\,dx$, $\displaystyle dv=e^x\,dx$ and $\displaystyle v=e^x$:

$$
\displaystyle \int x^3e^x\,dx=x^3e^x-\int3x^2e^x\,dx
=x^3e^x-3\int x^2e^x\,dx
$$

Repeating the process with $\displaystyle u=x^2$, $\displaystyle du=2x\,dx$, $\displaystyle dv=e^x\,dx$ and $\displaystyle v=e^x$:

$$
\displaystyle \int x^3e^x\,dx
=x^3e^x-3\left(x^2e^x-2\int xe^x\,dx\right)
$$

Finally, with $\displaystyle u=x$, $\displaystyle du=dx$, $\displaystyle dv=e^x\,dx$ and $\displaystyle v=e^x$:

$$
\displaystyle \begin{aligned}
\int x^3e^x\,dx
&=x^3e^x-3\left[x^2e^x-2\left(xe^x-\int e^x\,dx\right)\right]\\ 
&= x^3 e^x -3x^2 e^x + 6x\,e^x - 6 e^x + c \\
&= e^x \left(x^3 - 3x^2 + 6x - 6\right) + c
\end{aligned} 
$$

An efficient method for repeated integration by parts is to set up two columns: we successively differentiate the expression chosen as $\displaystyle u$ and successively integrate the expression accompanying $\displaystyle dx$ in $\displaystyle dv$. We multiply the terms along the diagonals, alternating the signs + and −. When the column of derivatives reaches zero, the process ends, as in the example below.

$$
\displaystyle \int x^3 e^x \, dx
$$

![Integration by parts using the tabular method for x³ eˣ](images/screenshot014.png)<br>
*Source: Created by the author (2025).*

$$
\displaystyle \begin{aligned}
\int x^3 e^x \, dx &= x^3e^x - 3x^2e^x + 6xe^x - 6e^x + \int 0\cdot e^x \,dx \\
&= e^x \left(x^3 - 3x^2 + 6x - 6\right) + c
\end{aligned}
$$

In this case, integration by parts led to a known integral. In other cases, the original integral reappears. When we obtain this repeated term, we can isolate it to solve the equation.

$$
\displaystyle \int e^x \sin{x} \, dx
$$

![Integration by parts with the integral of eˣ sin(x) reappearing](images/screenshot015.png)<br>
*Source: Created by the author (2025).*


$$
\displaystyle \begin{aligned}
\int e^x \sin{x} \, dx=e^x\sin{x}-e^x\cos{x}-\int e^x\sin{x} \,dx &\Rightarrow 2\int e^x \sin{x} \, dx=e^x\sin{x}-e^x\cos{x} + c\\
&\Rightarrow \int e^x \sin{x} \, dx=\frac{e^x\left(\sin{x}-\cos{x}\right)}{2} + c
\end{aligned}
$$

### Trigonometric Integrals:

The method depends on the factors and exponents present. In the parity cases below, the exponents are nonnegative integers.

**A power accompanied by the derivative of the function.** For the forms:

$$
\displaystyle \int\sin^m x\cos x\,dx
\qquad\text{ou}\qquad
\int\cos^m x\sin x\,dx,
$$

we substitute $\displaystyle u=\sin x$, with $\displaystyle du=\cos x\,dx$, or $\displaystyle u=\cos x$, with $\displaystyle du=-\sin x\,dx$, respectively.

**Even powers of sine or cosine.** For $\displaystyle \int\sin^m x\,dx$ or $\displaystyle \int\cos^m x\,dx$, with $\displaystyle m$ even, we use:

$$
\displaystyle \sin^2 x=\frac{1-\cos\left(2x\right)}2,
\qquad
\cos^2 x=\frac{1+\cos\left(2x\right)}2
$$

**Odd powers of sine or cosine.** If $\displaystyle m$ is odd, we separate a sine or cosine factor and use $\displaystyle \sin^2 x+\cos^2 x=1$, transforming the integral into the first case.

**Product of powers.** In $\displaystyle \int\sin^m x\cos^n x\,dx$, if either exponent is odd, we separate a factor of the corresponding function and use $\displaystyle \sin^2 x+\cos^2 x=1$. If both are even, we use the power-reduction identities above.

**Products with different arguments.** For $\displaystyle \sin\left(ax\right)\sin\left(bx\right)$, $\displaystyle \sin\left(ax\right)\cos\left(bx\right)$ or $\displaystyle \cos\left(ax\right)\cos\left(bx\right)$, we use the product-to-sum identities:

$$
\displaystyle \sin A\sin B=\frac12\left[\cos\left(A-B\right)-\cos\left(A+B\right)\right]
$$

$$
\displaystyle \cos A\cos B=\frac12\left[\cos\left(A-B\right)+\cos\left(A+B\right)\right]
$$

$$
\displaystyle \sin A\cos B=\frac12\left[\sin\left(A+B\right)+\sin\left(A-B\right)\right].
$$

**Powers of tangent or secant.** For $\displaystyle \int\tan^m x\,dx$ or $\displaystyle \int\sec^n x\,dx$, we use $\displaystyle \tan^2 x+1=\sec^2 x$ and/or integration by parts, depending on the case.

### Trigonometric Substitution:

With the identities $\displaystyle \sin^2\theta+\cos^2\theta=1$ and $\displaystyle \tan^2\theta+1=\sec^2\theta$, we use a right triangle to relate the radical expression to a trigonometric ratio. In the example below, the drawing represents $\displaystyle x>0$; the algebraic substitution also allows us to handle the other real values of $\displaystyle x$.

$$
\displaystyle \int \frac{dx}{\sqrt{4 + x^2}}
$$

![Reference triangle for the substitution x = 2 tan(θ)](images/screenshot016.png)<br>
*Source: Created by the author (2025).*

Analyzing the triangle:

$$
\displaystyle \tan\theta=\frac x2
\quad\Rightarrow\quad
x=2\tan\theta,\qquad dx=2\sec^2\theta\,d\theta
$$

$$
\displaystyle \begin{aligned}
\int\frac{dx}{\sqrt{4+x^2}}
&=\int\frac{2\sec^2\theta}{\sqrt{4+4\tan^2\theta}}\,d\theta\\
&=\int\frac{2\sec^2\theta}{\sqrt{4\left(1+\tan^2\theta\right)}}\,d\theta
\end{aligned}
$$

$$
\displaystyle \int\frac{2\sec^2\theta}{\sqrt{4\sec^2\theta}}\,d\theta
=\int\frac{2\sec^2\theta}{2\left\lvert\sec\theta\right\rvert}\,d\theta
$$

Since $\displaystyle \theta=\arctan\left(\frac{x}{2}\right)$, we have $\displaystyle -\frac{\pi}{2}<\theta<\frac{\pi}{2}$. Since, on this interval, for every $\displaystyle \theta$, $\displaystyle \sec \theta > 0$, then $\displaystyle \left|\sec \theta\right| = \sec \theta$. Therefore:

$$
\displaystyle \begin{aligned}
\int\frac{\sec^2\theta}{\left\lvert\sec\theta\right\rvert}\,d\theta
&=\int\frac{\sec^2\theta}{\sec\theta}\,d\theta\\
&=\int\sec\theta\,d\theta\\
&=\int\sec\theta\frac{\sec\theta+\tan\theta}{\sec\theta+\tan\theta}\,d\theta\\
&=\int\frac{\sec^2\theta+\sec\theta\tan\theta}{\sec\theta+\tan\theta}\,d\theta
\end{aligned}
$$

If $\displaystyle u=\sec\theta+\tan\theta$, then:

$$
\displaystyle du=\left(\sec^2\theta+\sec\theta\tan\theta\right)\,d\theta
$$

$$
\displaystyle \begin{aligned}
\int\frac{\sec^2\theta+\sec\theta\tan\theta}{u}\,
\frac{du}{\sec^2\theta+\sec\theta\tan\theta}
&=\int\frac{du}{u}\\
&=\ln\left\lvert u\right\rvert+c
\end{aligned}
$$

Returning to the previous variables:

$$
\displaystyle \ln\left\lvert u\right\rvert+c
=\ln\left\lvert\sec\theta+\tan\theta\right\rvert+c
=\ln\left|\frac{\sqrt{4+x^2}}2+\frac x2\right|+c
$$

$$
\displaystyle \int \frac{dx}{\sqrt{4 + x^2}} = \ln \left|\frac{\sqrt{4 + x^2}}{2} + \frac{x}{2}\right| + c
$$

### Partial Fractions:

This consists of separating a fraction with a polynomial denominator into a sum of simpler fractions.

$$
\displaystyle \int\frac{5x - 3}{x^2 - 2x - 3}\,dx= \int \frac{5x - 3}{\left(x+1\right)\left(x-3\right)}\,dx = \int\left(\frac{A}{x+1} + \frac{B}{x-3}\right)\,dx
$$

$$
\displaystyle 5x - 3= A\left(x-3\right) + B\left(x+1\right) \Rightarrow \left(A + B - 5\right)x +\left(-3A + B +3\right) = 0
$$

$$
\displaystyle \begin{cases}
A+B=5\\
-3A+B=-3
\end{cases}
\quad\Rightarrow\quad A=2,\ B=3
$$

Therefore:

$$
\displaystyle \int\left(\frac2{x+1}+\frac3{x-3}\right)\,dx
=2\ln\left\lvert x+1\right\rvert+3\ln\left\lvert x-3\right\rvert+c
$$

If the numerator polynomial $\displaystyle f\left(x\right)$ has degree greater than or equal to that of the denominator $\displaystyle g\left(x\right)$, we first perform polynomial division:

$$
\displaystyle \frac{f\left(x\right)}{g\left(x\right)} = m\left(x\right)+\frac{h\left(x\right)}{g\left(x\right)},\qquad \deg h<\deg g
$$

If there is a repeated linear factor in the denominator, we include all powers up to its multiplicity. For example:

$$
\displaystyle \frac{2x}{\left(x+1\right)^2}\text{, tem-se que: }\frac{2x}{\left(x+1\right)^2}=\frac{A}{x+1}+\frac{B}{\left(x+1\right)^2}
$$

If there is a quadratic factor irreducible over the reals, its numerator has the form $\displaystyle Bx+C$. For example:

$$
\displaystyle \frac{7x+2}{\left(x+2\right)\left(x^2+5\right)}\text{, tem-se que: } \frac{7x+2}{\left(x+2\right)\left(x^2+5\right)}= \frac{A}{x+2}+\frac{Bx+C}{x^2+5}
$$

---

# Appendix A. Supplementary Derivations

The derivations below complement the rules used in the handout. They can be consulted separately, without interrupting the sequence of chapters.

## Properties of Derivatives:

### Sum of Derivatives:

By the definition of the derivative, we separate the change in each function:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}\left[f\left(x\right)+g\left(x\right)\right]
&=\lim_{h\to0}\frac{\left[f\left(x+h\right)+g\left(x+h\right)\right]-\left[f\left(x\right)+g\left(x\right)\right]}{h}\\
&=\lim_{h\to0}\left[\frac{f\left(x+h\right)-f\left(x\right)}h+\frac{g\left(x+h\right)-g\left(x\right)}h\right]\\
&=f'\left(x\right)+g'\left(x\right)
\end{aligned}
$$

### Scalar Multiple of a Derivative:

Since $\displaystyle \alpha$ is constant, we can factor it out:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}\left[\alpha f\left(x\right)\right]
&=\lim_{h\to0}\frac{\alpha f\left(x+h\right)-\alpha f\left(x\right)}h\\
&=\alpha\lim_{h\to0}\frac{f\left(x+h\right)-f\left(x\right)}h\\
&=\alpha f'\left(x\right).
\end{aligned}
$$

### Product Rule:

The notion of linear approximation, $\displaystyle f\left(x+h\right)\approx f\left(x\right)+hf'\left(x\right)$ for small $\displaystyle h$, helps visualize why two terms arise. To follow the calculation using equalities, we add and subtract the term $\displaystyle f\left(x\right)g\left(x+h\right)$:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}\left[f\left(x\right)g\left(x\right)\right]
&=\lim_{h\to0}\frac{f\left(x+h\right)g\left(x+h\right)-f\left(x\right)g\left(x\right)}h\\
&=\lim_{h\to0}\left[
\frac{f\left(x+h\right)-f\left(x\right)}h g\left(x+h\right)
+f\left(x\right)\frac{g\left(x+h\right)-g\left(x\right)}h
\right].
\end{aligned}
$$

Since $\displaystyle g$ is differentiable, it is also continuous: $\displaystyle g\left(x+h\right)\to g\left(x\right)$. Thus:

$$
\displaystyle \frac{d}{dx}\left[f\left(x\right)g\left(x\right)\right]=f'\left(x\right)g\left(x\right)+f\left(x\right)g'\left(x\right).
$$

### Quotient Rule:

With $\displaystyle g\left(x\right)\ne0$, we combine the fractions and rearrange the numerator:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}\frac{f\left(x\right)}{g\left(x\right)}
&=\lim_{h\to0}\frac{\frac{f\left(x+h\right)}{g\left(x+h\right)}-\frac{f\left(x\right)}{g\left(x\right)}}h\\
&=\lim_{h\to0}\frac{f\left(x+h\right)g\left(x\right)-f\left(x\right)g\left(x+h\right)}{h\,g\left(x+h\right)g\left(x\right)}\\
&=\lim_{h\to0}
\frac{g\left(x\right)\frac{f\left(x+h\right)-f\left(x\right)}h-f\left(x\right)\frac{g\left(x+h\right)-g\left(x\right)}h}
{g\left(x+h\right)g\left(x\right)}\\
&=\frac{f'\left(x\right)g\left(x\right)-f\left(x\right)g'\left(x\right)}{g^2\left(x\right)}.
\end{aligned}
$$

### Chain Rule:

In the composition $\displaystyle f\left(g\left(x\right)\right)$, a small change in $\displaystyle x$ first changes $\displaystyle g$ and then $\displaystyle f$. By linear approximation:

$$
\displaystyle \Delta g=g\left(x+h\right)-g\left(x\right)\approx g'\left(x\right)h,
$$

$$
\displaystyle f\left(g\left(x+h\right)\right)-f\left(g\left(x\right)\right)\approx f'\left(g\left(x\right)\right)\Delta g.
$$

Combining the two relations, the change in the composition is approximated by $\displaystyle f'\left(g\left(x\right)\right)g'\left(x\right)h$. Dividing by $\displaystyle h$, we obtain the intuition for the rule:

$$
\displaystyle \frac{d}{dx}f\left(g\left(x\right)\right)=f'\left(g\left(x\right)\right)g'\left(x\right).
$$

> This explanation uses approximations to show the idea behind the rule. When $\displaystyle f$ and $\displaystyle g$ are differentiable at the points involved, the errors in these approximations, divided by $\displaystyle h$, tend to zero; therefore, the limit gives the indicated equality.

## Important Derivatives:

### Constant:

For any real constant $\displaystyle c$:

$$
\displaystyle \frac{d}{dx}c=\lim_{h\to0}\frac{c-c}h=\lim_{h\to0}\frac0h=0.
$$

### Sine:

Using the sine addition formula:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}\sin x
&=\lim_{h\to0}\frac{\sin\left(x+h\right)-\sin x}h\\
&=\lim_{h\to0}\frac{\sin x\cos h+\cos x\sin h-\sin x}h\\
&=\sin x\lim_{h\to0}\frac{\cos h-1}h
+\cos x\lim_{h\to0}\frac{\sin h}h.
\end{aligned}
$$

To determine these limits, we can use trigonometry. We consider a circle of radius 1, with an angle $\displaystyle \theta$ in radians, and compare the areas of a circular sector and two triangles:

![Unit circle and area comparison for the limit of sin(θ)/θ](images/screenshot017.png)<br>
*Source: Created by the author (2025).*

For $\displaystyle 0<\theta<\frac{\pi}{2}$, the areas of the smaller triangle, circular sector, and larger triangle are, respectively, $\displaystyle \frac12\cos\theta\sin\theta$, $\displaystyle \frac12\theta$ and $\displaystyle \frac12\tan\theta$. Thus:

$$
\displaystyle \frac12\cos\theta\sin\theta\le\frac12\theta\le\frac12\tan\theta.
$$

Dividing and taking reciprocals of the positive ratios:

$$
\displaystyle \cos\theta\le\frac{\theta}{\sin\theta}\le\frac1{\cos\theta}
\quad\Rightarrow\quad
\cos\theta\le\frac{\sin\theta}{\theta}\le\frac1{\cos\theta}.
$$

The two outer functions tend to 1. By the squeeze theorem, the right-hand limit is 1; since $\displaystyle \frac{\sin\left(-\theta\right)}{-\theta}=\frac{\sin\theta}{\theta}$, the left-hand limit is the same:

$$
\displaystyle \lim_{\theta\to0}\frac{\sin\theta}{\theta}=1.
$$

For the other limit, we rationalize:

$$
\displaystyle \begin{aligned}
\frac{\cos\theta-1}{\theta}
&=\frac{\left(\cos\theta-1\right)\left(\cos\theta+1\right)}{\theta\left(\cos\theta+1\right)}\\
&=-\frac{\sin^2\theta}{\theta\left(\cos\theta+1\right)}\\
&=-\frac{\sin\theta}{\theta}\frac{\sin\theta}{\cos\theta+1}.
\end{aligned}
$$

Therefore:

$$
\displaystyle \lim_{\theta\to0}\frac{\cos\theta-1}{\theta}
=-1\cdot\frac02=0.
$$

Returning to the derivative:

$$
\displaystyle \frac{d}{dx}\sin x=\sin x\cdot0+\cos x\cdot1=\cos x.
$$

### Cosine:

Using the cosine addition formula and the previous limits:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}\cos x
&=\lim_{h\to0}\frac{\cos\left(x+h\right)-\cos x}h\\
&=\lim_{h\to0}\frac{\cos x\cos h-\sin x\sin h-\cos x}h\\
&=\cos x\lim_{h\to0}\frac{\cos h-1}h
-\sin x\lim_{h\to0}\frac{\sin h}h\\
&=\cos x\cdot0-\sin x\cdot1=-\sin x.
\end{aligned}
$$

### Exponential:

For $\displaystyle a>0$, we factor out $\displaystyle a^x$:

$$
\displaystyle \frac{d}{dx}a^x
=\lim_{h\to0}\frac{a^{x+h}-a^x}h
=a^x\lim_{h\to0}\frac{a^h-1}h.
$$

Here we use the fundamental exponential limit $\displaystyle \lim_{u\to0}\frac{e^u-1}{u}=1$. Taking this limit as known, we write $\displaystyle a^h=e^{h\ln a}$. For $\displaystyle a\ne1$, with $\displaystyle u=h\ln a$:

$$
\displaystyle \begin{aligned}
\lim_{h\to0}\frac{a^h-1}h
&=\lim_{h\to0}\frac{e^{h\ln a}-1}{h\ln a}\ln a\\
&=1\cdot\ln a=\ln a.
\end{aligned}
$$

Therefore:

$$
\displaystyle \frac{d}{dx}a^x=a^x\ln a.
$$

If $\displaystyle a=1$, the function is constant and the formula also gives zero.

**Frequently used:** $\displaystyle \frac{d}{dx}e^x=e^x\ln e=e^x$.

### Logarithm:

The logarithm $\displaystyle \log_a x$ is the inverse function of $\displaystyle f\left(x\right)=a^x$, with $\displaystyle a>0$, $\displaystyle a\ne1$ and $\displaystyle x>0$. Applying the derivative of the inverse:

$$
\displaystyle \frac{d}{dx}\log_a x
=\frac1{f'\left(f^{-1}\left(x\right)\right)}
=\frac1{a^{\log_a x}\ln a}
=\frac1{x\ln a}.
$$

**Frequently used:** $\displaystyle \frac{d}{dx}\ln x=\frac{1}{x}$.

### Power Rule:

The derivation using the binomial theorem below assumes that $\displaystyle n$ is a positive integer. We begin with:

$$
\displaystyle \frac{d}{dx}x^n=\lim_{h\to0}\frac{\left(x+h\right)^n-x^n}h.
$$

The binomial theorem allows us to expand:

$$
\displaystyle \left(x+h\right)^n=\sum_{k=0}^{n}\binom nk x^{n-k}h^k,
\qquad
\binom nk=\frac{n!}{k!\left(n-k\right)!}.
$$

The coefficients are:

$$
\displaystyle \binom n0=\frac{n!}{0!n!}=1,
\qquad
\binom n1=\frac{n!}{1!\left(n-1\right)!}=n,
\binom n2=\frac{n\left(n-1\right)}2,
$$

$$
\qquad\cdots\qquad
$$

$$
\binom n{n-2}=\frac{n\left(n-1\right)}2, \qquad
\displaystyle \binom n{n-1}=n,
\qquad
\binom nn=1.
$$

The terms with index 2 assume $\displaystyle n\ge2$. Applying the expansion to the derivative:

$$
\displaystyle \begin{aligned}
\frac{d}{dx}x^n
&=\lim_{h\to0}
\frac{x^n+nx^{n-1}h+\binom n2x^{n-2}h^2+\cdots+h^n-x^n}h\\
&=\lim_{h\to0}
\left[nx^{n-1}+\binom n2x^{n-2}h+\cdots+h^{n-1}\right]\\
&=nx^{n-1}.
\end{aligned}
$$

For $\displaystyle n=1$, the calculation is directly $\displaystyle \lim_{h\to0}\frac{h}{h}=1$. For real exponents with $\displaystyle x>0$, we can use $\displaystyle x^n=e^{n\ln x}$ and the rules already presented:

$$
\displaystyle \frac{d}{dx}x^n=e^{n\ln x}\frac nx=nx^{n-1}.
$$

## Fundamental Theorem of Calculus:

### Mean Value Theorem for Integrals:

Given a function $\displaystyle f$ continuous on $\displaystyle \left[a,b\right]$, with $\displaystyle a<b$, its integral can be equated to the contribution of a rectangle of base $\displaystyle b-a$ and height $\displaystyle f\left(c\right)$, for some $\displaystyle c\in\left[a,b\right]$:

$$
\displaystyle \int_a^b f\left(x\right)\,dx=f\left(c\right)\left(b-a\right).
$$

This comparison considers signed area: a negative height represents a negative contribution.

![Mean value theorem for integrals and signed area](images/screenshot018.png)<br>
*Source: Created by the author (2025).*

### Relationship Between Integrals and Antiderivatives:

Defining $\displaystyle G\left(x\right)=\int_a^x f\left(t\right)\,dt$, we can understand $\displaystyle G\left(x\right)$ as the signed area accumulated from $\displaystyle a$. We use $\displaystyle t$ inside the integral to distinguish it from the variable endpoint $\displaystyle x$:

$$
\displaystyle 
G'\left(x\right)
=\lim_{h\to0}\frac{G\left(x+h\right)-G\left(x\right)}h=\lim_{h\to0}
\frac{\int_a^{x+h}f\left(t\right)\,dt-\int_a^x f\left(t\right)\,dt}{h}.
$$

![Change in accumulated area between x and x + h](images/screenshot019.png)<br>
*Source: Created by the author (2025).*

As seen in the image, the difference corresponds to the portion between $\displaystyle x$ and $\displaystyle x+h$:

$$
\displaystyle \int_a^{x+h}f\left(t\right)\,dt-\int_a^x f\left(t\right)\,dt
=\int_x^{x+h}f\left(t\right)\,dt.
$$

According to the mean value theorem for integrals, there is a point $\displaystyle c_h$ between $\displaystyle x$ and $\displaystyle x+h$ such that:

$$
\displaystyle \int_x^{x+h}f\left(t\right)\,dt=f\left(c_h\right)h.
$$

Thus:

$$
\displaystyle G'\left(x\right)=\lim_{h\to0}\frac{f\left(c_h\right)h}h=\lim_{h\to0}f\left(c_h\right).
$$

As $\displaystyle h\to0$, the interval containing $\displaystyle c_h$ shrinks and $\displaystyle c_h\to x$, both for $\displaystyle h>0$ and for $\displaystyle h<0$. By the continuity of $\displaystyle f$, we conclude that $\displaystyle G'\left(x\right)=f\left(x\right)$.

With $\displaystyle F$ an antiderivative of $\displaystyle f$, we have:

$$
\displaystyle \frac{d}{dx}\left[F\left(x\right)-G\left(x\right)\right]=f\left(x\right)-f\left(x\right)=0.
$$

Therefore, $\displaystyle F\left(x\right)-G\left(x\right)$ is constant on the interval. Calling this constant $\displaystyle C$:

$$
\displaystyle F\left(x\right)-G\left(x\right)=C
\quad\Rightarrow\quad
G\left(x\right)=F\left(x\right)-C.
$$

Since $\displaystyle G\left(a\right)=\int_a^a f\left(t\right)\,dt=0$, we have $\displaystyle C=F\left(a\right)$. Therefore:

$$
\displaystyle G\left(x\right)=F\left(x\right)-F\left(a\right).
$$

Defining an endpoint $\displaystyle b\ge a$, we obtain:

$$
\displaystyle \int_a^b f\left(x\right)\,dx=F\left(b\right)-F\left(a\right).
$$

---

# Sources:

- STEWART, James. *Cálculo: volume 1*. 7th ed. São Paulo: Cengage Learning, 2013.

- THOMAS, George B.; WEIR, Maurice D.; HASS, Joel. *Cálculo: volume 1*. 12th ed. São Paulo: Pearson, 2012.

- THOMAS, George B.; WEIR, Maurice D.; HASS, Joel. *Cálculo: volume 2*. 12th ed. São Paulo: Pearson, 2012.

- LIMA, Elon Lages. *Análise real: volume 1*. 8th ed. Rio de Janeiro: IMPA, 2006.

- TAKHE. *Cálculo 1: aulas e exercícios resolvidos*. [YouTube], 2023. Available at: [https://www.youtube.com/playlist?list=PLmAu9dltGZtp1apl5ib_uL7UkP4fDwy4m](https://www.youtube.com/playlist?list=PLmAu9dltGZtp1apl5ib_uL7UkP4fDwy4m). Accessed: September 10, 2025.

- BARROS, Tatiana Leal. *Course: Calculus with functions of one real variable*. Undergraduate program in Computer Engineering — Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2024.

- CAMARGO JUNIOR, Fausto de. *Course: Integration and series*. Undergraduate program in Computer Engineering — CEFET-MG, 2024.

## When $1 + 1 \neq 2$ (and the mathematics of pulleys)

![initial-problem](blog-website/notes/Physics/PulleySystem1/initial-problem.png)

Let's say we have a pulley system as shown in the image. All the ropes involved are massless and inextensible, there is no friction anywhere and the pulley itself has no mass. Blocks (1) and (2) have masses $m_1$ and $m_2$ respectively, where $m_1 < m_2$.

We want to know what force is acting on the ceiling, i.e. how much the system is pulling down on it. You might think that the answer is trivially $\vec{F} = m_1\vec{g} + m_2\vec{g}$, but you would be wrong!

To see why this is intuitively, consider what would happen in the case when $m_1 = m_2$. The two objects would stay still, and $\vec{F} = m_1\vec{g} + m_2\vec{g}$ would indeed be the correct answer. Now, what happens when the two masses are accelerating? Since the heavy mass is moving down, and the light mass is moving up, the center of mass of the system (the "average mass") is moving down. Because of this, it's almost as if the ceiling can't hold the system still and it's "half-stopping" it. The system (the center of mass) accelerates toward the ground with an acceleration of $0\lt a_{cm} \lt g$. The system is "half-falling". And so, the force on the ceiling has to be less than the sum of the weights of the objects! Otherwise, the center of mas would have to be motionless.

*From here, I will try to be as rigorous as possible when treating the pulley system. Such mathematical rigor will be useful when dealing with systems that are much more complex than this. This is more of a reference for the future.*

To investigate what's happening, we need to write down Newton's second law for all the objects involved. First let's talk about some basic notions. Because the strings and the pulley are all massless, the sum of the forces acting on them must be 0, since otherwise they would have infinite acceleration (more on this later). This leads to the well-known fact that *"tension is constant along massless strings"* *, as shown on the picture.

Since $m_1 < m_2$, it is obvious that object (1) will start ascending, and object (2) will start descending. It's also reasonable to assume that the strings will all remain taut (extended all the way) during the motion. We can conclude this just by experience: blocks hanging on strings don't usually start jumping around by themselves. But, strictly theoretically speaking, this is because such idealised (inextensible) ropes produce tension that is **exactly** enough to keep the rope at constant length. Since both objects want to fall *down*, the rope has to have non-zero tension to keep the left object moving up. Again, more on this later when we cover kinematics.

The fact that the rope is always taut and that it cannot change length gives us a way to make a connection between the vertical accelerations of the two blocks. We will try to solve this problem as formally as possible, making almost no assumptions about any vector directions. This will show that problems like this can be solved algorithmically, with little to no intuitive thinking.

Let's look at the following illustration:
![moving](blog-website/notes/Physics/PulleySystem1/moving.png)

We are looking at the system at time 0 and at time $t \neq 0$ . During this time, the left block moved up, and the right block moved down. Their respective displacement vectors are $\Delta\vec{y}_1(t) = \vec{y}_1(t) - \vec{y}_1(0)$ and $\Delta\vec{y}_2(t) = \vec{y}_2(t) - \vec{y}_2(0)$. Let's call the length of the rope $L$. Let $C$ be the length of the curved rope resting on top of the pulley. This length is constant during the motion (at least until block (1) reaches the bottom of the pulley). $\vec{L}_1(t)$ and $\vec{L}_2(t)$ are the vectors connecting the blocks with the points at which they're hanging from on the pulley.

It's obvious from the picture that $L = C + |\vec{L}_1(t)| + |\vec{L}_2(t)|$. We can also see that $\vec{y}_1(t) + \vec{L}_1(t) = \vec{const}_1$ and $\vec{y}_2(t) + \vec{L}_2(t) = \vec{const}_2$, where $\vec{const}_1$ and $\vec{const}_2$ correspond to height-vectors of the hanging points (they are constant, of course). Differentiating these last two equations w.r.t time, we get:  $\ddot{\vec{L}}_1(t)=-\ddot{\vec{y}}_1(t)$ and $\ddot{\vec{L}}_2(t)=-\ddot{\vec{y}}_2(t)$. Now let's differentiate the first equation (involving $L$ and $C$) once:

We get $\vec{0}=\hat{L}_1(t)\cdot\dot{\vec{L}}_1(t)+\hat{L}_2(t)\cdot\dot{\vec{L}}_2(t)$ (will explain later why). Now, because the blocks can only move up and down, $\hat{L}_1(t)$ and $\hat{L}_2(t)$ don't rotate, they stay vertical. Also, because the left block won't hit the pulley and the right block won't hit the floor, these two vectors will not reverse directions. We therefore get that $\hat{L}_1(t)=\hat{L}_2(t)=\hat{j}$. This was probably the only observation/assumption needed to make during this calculation.

Now we differentiate  $\vec{0} =\hat{j}\cdot(\dot{\vec{L}}_1(t)+\dot{\vec{L}}_2(t))$ again. Using the vector chain rule and taking into consideration that $\dot{\hat{j}}=\vec{0}$, we get that $\vec{0} =\hat{j}\cdot(\ddot{\vec{L}}_1(t)+\ddot{\vec{L}}_2(t))$. Now substituting from before, we get: $\vec{0} =\hat{j}\cdot(\ddot{\vec{y}}_1(t)+\ddot{\vec{y}}_2(t))$. This is what we were looking for! From here, $\ddot{\vec{y}}_1(t)\cdot\hat{j}=-\ddot{\vec{y}}_2(t)\cdot\hat{j}$.

Let us finally write down Newton's second law:

$$
\begin{align*}
  \vec{T}+m_1\vec{g}=m_1\ddot{\vec{y}}_1(t) \\ 
  \vec{T}+m_2\vec{g}=m_2\ddot{\vec{y}}_2(t)
\end{align*}
$$
Now, let's multiply (dot product) both sides of the equations with $\hat{j}$ (equivalent to writing Newton's second law for the vertical axis) and substitute:

$$
\begin{align*}
  |\vec{T}|-m_1|\vec{g}|=m_1(\ddot{\vec{y}}_1(t)\cdot\hat{j})=m_1(-\ddot{\vec{y}}_2(t)\cdot\hat{j}) \\ 
  |\vec{T}|-m_2|\vec{g}|=m_2(\ddot{\vec{y}}_2(t)\cdot\hat{j})
\end{align*}
$$


Now we subtract the equations:

$$(m_2-m_1)|\vec{g}|=-(m_1+m_2)\ddot{\vec{y}}2(t)\cdot\hat{j}=-(m_1+m_2)a_{2y}$$

After some additional calculations we get:
$$
\begin{align*}
a_{1y}=\frac{m_2 - m_1}{m_1+m_2}|\vec{g}| \\
a_{2y}=\frac{m_1-m_2}{m_1+m_2}|\vec{g}| \\
|\vec{T}|=\frac{2m_1m_2}{m_1+m_2}|\vec{g}|
\end{align*}
$$
Note that because $m_1 < m_2$, from the above formulas we get that $a_{1y} \gt 0$ and $a_{2y} \lt 0$, which makes sense.

To find the force by which the ceiling is acting on the system, we look at the following diagram:

![center-of-mass](blog-website/notes/Physics/PulleySystem1/center-of-mass.svg)

We see that the ceiling is acting on the system with the force $\vec{F}=-2\vec{T}=-2|\vec{T}|\hat{j}$. Let's set tight bounds on $|\vec{F}|$ and $a_{cm}$. 
$$(m_1+m_2)|\vec{g}|-\frac{4m_1m_2}{m_1+m_2}|\vec{g}|=\frac{(m_1-m_2)^2}{m_1+m_2}|\vec{g}|\gt0$$
And so we get:
$$0\lt\frac{4m_1m_2}{m_1+m_2}|\vec{g}|\lt(m_1+m_2)|\vec{g}|$$
So we see that, indeed, the pull on the ceiling is **less** than the sum of the weights of the objects! Let's do the same for $a_{cm}$. We write down Newton's second law for the whole pulley system (blocks + pulley):

$$2\vec{T}+(m_1+m_2)\vec{g}=(m_1+m_2)\vec{a}_{cm}$$

$$0 \lt a_{cm_{y}}=(\frac{m_1-m_2}{m_1+m_2})^2|\vec{g}| \lt g$$
These bounds also agree with our qualitative explanations in the beginning of the page. 

What did we learn? Don't over complicate things when you don't have to.


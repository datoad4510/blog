$$\int_{-\infty}^{\infty} \int_0^1 ( {\color{red}x_1} + x_2)dx_1dx_2$$
Let's define a generalised interval $\langle a,b \rangle$. $\langle a,b\rangle$ could be either one of the following: $[a,b], (a,b), [a,b), (a,b]$. 

Now let's define a length function on these intervals $L:\langle a,b \rangle \mapsto \mathbb{R}$ as follows:


1.) $L(\emptyset) = 0$

2.) $L(\langle a,b\rangle) = \lvert b - a \rvert$

3.) $L(\bigcup\limits_{i=1}^{n} A_{i}) =\sum \limits_{i=1}^{n} L(A_{i}), \forall n \in \mathbb{N} \setminus \{ 0 \},$ where $A_{i}$ are pairwise disjoint generalised intervals.

Based on the abovementioned axioms, is it possible to prove the following statement:

$L(\bigcup\limits_{i=1}^{n} A_{i}) \le \sum \limits_{i=1}^{n} L(A_{i}), \forall n \in \mathbb{N} \setminus \{ 0 \},$ where $A_{i}$ are generalized intervals.

It seems to me that this should be very  easy to prove, but I've been unable to do it... I want to construct an axiomatic approach to "length" (avoiding the Lebesgue measure).

I was trying to do it using an induction argument. We can see that for n=2, if the two generalised intervals don't intersect or intersect at a single point, then by axioms 2 and 3 equality holds. Now if the intersection is greater than a single point, we know that $A \cup B = (A \setminus (A \cap B)) \cup (B \setminus (A \cap B))$, where $A \setminus (A \cap B)$ and $B \setminus (A \cap B)$ are disjoint. But I can't really progress from here.

Are there any additional axioms needed to prove the theorem? What if I added an axiom for set difference for example, would that be enough?

<font style="color:green">Text Color</font>
```note-orange
asdaddasd
```
[GitHub - deathau/obsidian-snippets](https://github.com/deathau/obsidian-snippets#notation-colour-blocks)

[sugma]

```cpp
#include <iostream>

using namespace std;

int main() {
	cout << "Hello world!" << endl;
	return 0;
}
```

==sugon==

![[content/notes/images/example.png]]
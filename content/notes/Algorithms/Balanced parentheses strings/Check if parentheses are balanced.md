# Balanced parentheses language

Balanced parentheses language $L$:  

1. $\varepsilon \in L$. 
2. If $S \in L$, then $(S) \in L$.
3. If $A \in L$ and $B \in L$, then $AB \in L$.

A parentheses string is called balanced if it's in the balanced parentheses language.

# Helpful lemmas

**Lemma 0:** If a string is balanced, then its length is even (and the number of (-s is the same as the number of )-s ).

**Proof:** Strong induction on the length of strings. Consequently, the number of parentheses in a balanced string is even.

**Lemma 1:** Every balanced parentheses string (except for the empty string) contains at least one ().

**Proof:** Strong induction on the length of strings.

**Lemma 2:** Removing a () from a balanced string leaves the string balanced.

**Proof:** Strong induction on the length of strings.

**Lemma 3 (converse of Lemma 2):** If after removing a () from a string, the resulting string was balanced, then the initial string was also balanced.

**Proof:**

This is the same as saying that if we insert a () anywhere into a balanced string, the resulting string will also be balanced. Again, this can be done using strong induction on the length of the strings and casework:

If S = (A):
()(A), (A)(),(()A),(A()),(B)

Here, B corresponds to the string that we get if we add the () inside the A.

If S = AB, where both are nonempty:
()AB,A()B,AB(),CB,AD

Same with C and D as before with B.

You can write down the proofs yourself to make sure that they're true.

# Algorithm
Keep removing ()-s from the string until we can't. If, at the end, the string isn't empty, we return false. If it's empty, then we return true.

# Correctness
Termination conditions: 

1.) The initial string is balanced:

If a string is balanced, according to Lemma 0 it has an even number of characters $2n$. Using Lemma 1 and Lemma 2, we can keep removing ()-s from it with the result always being balanced. Since the number of characters is even and keeps decreasing by 2, after $n$ steps the string will become empty. The algorithm, as expected, returns true.

2.) The initial string is unbalanced:

After a few steps, the resulting string must not have a () and be non empty. Otherwise, the initial string would have been balanced by repeated application of Lemma 3 (basically adding back in the removed pairs of parentheses), which is a contradiction. Therefore the algorithm returns false, as expected.

This means that the algorithm is correct. It returns the correct answer in both cases.

Let $L_1$ be the language of all balanced parentheses. Let $L_2$ be the language of our algorithm (set of strings for which it returns true). We showed that for every balanced string $s \in L_1$, the algorithm returns true. I.e. $s \in L_1 \implies s \in L_2$. We also showed that for every unbalanced string $s \notin L_1$, the algorithm returns false, i.e. $s \notin L_2$. $s \notin L_1 \implies s \notin L_2$. Negating this statement, we get $s \in L_2 \implies s \in L_1$. Together with the first logical statement above, we get $s \in L_1 \iff s \in L_2$. These two languages are equivalent.

# Optimising using a stack

We need to optimise this further, because removing substrings from s string is slow.

Let's keep removing the leftmost () pair. This is fine because it doesn't really matter which () we remove at some point during the algorithm (Lemmas 2 and 3 make no assumption on the position of this pair).

**Lemma 4:** In a balanced string the leftmost closing bracket must have an opening bracket before it (part of the leftmost pair).

**Proof:** This is equivalent to saying that a balanced string starts with at least one opening bracket. Suppose the opposite. Let's say some balanced string starts with a closing bracket. Our previous algorithm must result in an empty string when used on the given string. However, if at any point during the algorithm there is a ) at the start of the string, we know that it will never be removed, because a ( will never be magically added in front of it. Hence the algorithm will not return an empty string, this is a contradiction. Our assumption was wrong.

**Idea:** As a converse of Lemma 4, if the string starts with a closing bracket, then it must be unbalanced.

Lemma 4 and the idea above must apply to the string during every step of the algorithm (before and after removing the leftmost () pair).

So, we can move left to right in the string using a pointer. If we encounter a closing bracket, according to Lemma 4, it must be part of the leftmost pair, so we remove it. We need to keep a list of opening brackets before it that haven't been paired up yet. The closing bracket we encountered will be paired up with the rightmost of these opening brackets and both will be removed.

This list of available opening brackets can be kept using a stack. The top of the stack corresponds to the rightmost available open bracket, so we pop it (remove the opening bracket) and advance the pointer (remove the closing bracket). If we come across an opening bracket, add it to the stack. If the stack is empty and our pointer points to a closing bracket, that means it cannot be removed. Then, according to the idea and the negation of Lemma 3, the initial string must have been unbalanced. Also, when our pointer finishes traversing the string, if the stack is not empty, then that means some of the opening brackets were not removed, the string looks like (((((, and is obviously unbalanced (Lemma 0). This means that the initial string was also unbalanced (negation of Lemma 3).

# Further optimisation (constant space)
Notice that, since the opening brackets in the stack are all the same, we don't really need to store the information about which opening bracket was the rightmost one. We could just keep a count of opening brackets. Increment it when we come across an opening bracket (push) and decrement it when we come across a closing bracket (pop). If the count ever becomes negative (stack is empty and we came across a closing bracket), we know the string was unbalanced. At the end, make sure that count is zero (stack is empty).

This optimisation will not work when there are different types of parentheses in the string, because now it matters which type of bracket was on the top of the stack!
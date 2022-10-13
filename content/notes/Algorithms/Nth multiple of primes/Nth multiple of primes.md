### Nth multiple of primes

We are given a sorted array of prime numbers $P$, and an integer $n$. We want to find the nth largest number whose prime factors are in $P$. In other words, we have the following sets:

 $$\begin{array}{l}
 S=\{\; p_{1}^{k_1}p_{2}^{k_2}...p_{m}^{k_m} \;| \;k_1,\;k_2,\;...,\;k_m \ge 0 \; \text{and} \; \;p_1,\;p_2,\;...,\;p_m \in P \; \} \\[4ex]
 P=\{ \; p_1,\;p_2,\;...,\;p_m \; \}, \; \text{where every element of P is prime. $m \ge 1$.}
 \end{array}$$
 
 Now, if we order the elements of $S$ from the smallest (which would be 1, $k_1=\;k_2=\;...=\;k_n=0$) to the largest, we want the nth number in this ordering.
 
**Example: 

Let's say $P=[2,3,5]$ and $n=5$. Then the numbers in increasing order are:
$$1 == 2^03^05^0,2 == 2^13^05^0,3 == 2^03^15^0, 4 == 2^23^05^0, 5 == 2^03^05^1$$
We return the fifth number, is 5.

### Solution

Let $A$ be an array consisting of $n$ elements, where $A[i]$ is the i-th largest element of $S$. Let $P$ be the array containing the prime number factors in sorted order.

It's easy to notice that there is a kind of recursive pattern among these numbers. For example, $2^13^15^0==2*2^03^15^0$. So, by moving one of the primes out of the number, we are still left with a number whose prime factors are 2,3 and 5. Additionally, this new number is strictly smaller than the initial one. This means that the new number is an element of $A$ and its index is less than the index of the initial number.

This gives us hope that there might be a recursive formula for finding $A[n]$ using the values $A[n]/2,A[n]/3$ and $A[n]/5$. The problem is, we still have no idea where exactly in $A$ these three numbers are. We need recursive calls that decrease the index, which we don't have.

So, what else do we know? Let's get back to the idea that $A[n]$ is equal to $A[i]$ times 2,3 or 5, where $1 \le i \lt n$. We can go over the whole array and multiply each of the elements by 2,3 and 5. We basically generate every candidate answer. $A[n]$ must be one of these by the method of exhaustion. But which one? Well, if any of the candidates are already in $A$, we can skip them, because $A[n]$ is strictly greater than all the elements before it. Every candidate that's left is an element of $S$, and all of them come after $A[n-1]$ in the ordering. Because we want the immediate successor of $A[n-1]$, we take the minimum of what's left of the candidates. This will be the answer!

Calculating all the candidates will take $O(n*|P|)$ time. Finding the minimum will have the same time complexity. Since we are building the $A$ array incrementaly, we will need to do this $n$ times. So, the final time complexity will be $O(n^2*|P|)$.

Notice that we are doing extra work: every time we calculate the canidate answers, we just throw them out instead of reusing them in the future. There also might be a way to quickly skip all the candidates that are already in $A$. This gives us motivation to try and optimize the solution.

We can keep all the valid candidates in an ordered set. An ordered set automatically filters duplicates. At every step of the algorithm, we calculate the next element of the array by taking the minimum of the set. We then erase this element from the set, because it will never be a valid candidate in the future. We then look at this element times 2,3 and 5, and add only the valid ones out of these three to the set (Every other element times 2,3,5 was already considered and appropriately filtered when it was added to the array!). How do we know which of these three are valid? We also keep a hashset of all the previous elements of the array, and so we just check which of them are in the hashset. After calculating the next element in the array, we also add it to the hashset.

The time complexity all the set operations during each step of the algorithm will be $O(|P|log(n))$. There will be $n$ steps, so the total time complexity will be $O(|P|n*log(n))$. The space complexity will be $O(n)$.

Surprisingly, there is also a $O(n*log(|P|))$ time complexity solution. This solution hinges on the observation that every time we are calculating the next element of the array, we know that there are exactly $|P|$ possible candidates. At each step of the algorithm, $A[i]$ is the minimum of these candidates. After finding it, we replace it in the candidate list with another candidate. We keep the candidates in a heap, which  explains the logarithm in the time complexity.

So, which candidates are these? Well, we know that $A[1] == 1$. Based on this, the possible candidates for $A[2]$ are 2,3 and 5. We take the minimum of these three, which is 2. So, two is not going to be in the candidate space for $A[3]$. So now we have ${\color{red}2*2,}{\color{cyan}3*1,3*2,}{\color{green}5*1,5*2}$ as the candidates. Notice how we grouped them. Now, we know that $3*2$ and $5*2$ cannot be $A[3]$, because we have smaller values in the candidate space, namely $3*1$ and $5*1$.

The minimum of these is 3, we remove it from the list and add more candidates:
$${\color{red}2*2,2*3,}{\color{cyan}3*2,3*3,}{\color{green}5*1,5*2,5*3}$$
Each of the goups corresponds to new candidates generated by previous elements. The first group corresponds to an element times 2, the second one times 3 and so on. What can we say about these groups? We know that at every step of the algorithm, all of the candidates in these groups were generated by older elements of the array. These older elements were strictly less than the one we are currently calculating! So, when we add the current element times 2 to the first group, for example, it will be greater than the candidates in the same group before it (if $x \lt y$, then $2x \lt 2y$). Same goes for all the other primes. This means that, the groups will be in sorted order at every point in time!

Because the groups are sorted, we know that the smallest candidate must be one of the starting values of the three groups ($|P|$ groups in general). So, we just keep these values in a heap, as we already stated above. When we are calculating a candidate, we remove one item from the lists (minimum of the groups) and then add $|P|$ candidates. All in all, at every step, we add $|P|-1=O(|P|)$ number of elements. Since this will be done $n$ times, the space complexity of this algorithm will be $O(|P|*n)$.
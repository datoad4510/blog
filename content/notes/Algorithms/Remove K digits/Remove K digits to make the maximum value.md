# Remove K digits

We are given an array of digits $digits[1..n]$, and a number $k$. We must remove k digits from the array. The remaining digits will then correspond to a decimal number. We want this number to be as large as possible. Which digits do we remove?

### Examples:

$digits=[4,5,2,1]$, k = 2. We remove the 4 and the 1 and are left with the number 45.

## Solution:

First notice that removing k digits is equivalent to leaving n-k digits. So let's choose which n-k digits we want to leave.

### Brute force solution

Just look at all (n-k)-length subsequences of $digits$ and return the one that corresponds to the largest number. This will take about $n * C_{n-k}^{n}=n*C_{k}^{n}$ operations. Choose any of the digits as the first digit in the final number and recurse to the right. If there aren't enough digits left, it's an invalid solution.

### Optimizing the brute force solution

When recursing to the right, we are doing repeated work. Define $m = n-k$. The recursion looks like this:

$$\text{removeK}(digits[1..n],m)=max_{i \in [1..n]}(digits[i]*10^m +\text{removeK}(digits[i+1..n],m-1))$$
$$\text{removeK}(digits[i..n],m)=0,\text{ }n-i+1 \lt m \text{ (can't choose m digits, less than m left)}$$
$$\text{removeK}(digits[i..n],0)=0,\text{ (done with choosing)}$$

We can make a 2D table to cache solutions to the subproblems. The time complexity will be $O(n * k)$, and the space complexity will also be $O(n * k)$.

### Optimal solution

Let's first think about the decision space for the problem. We are first going to choose the first digit of the final number, then the second, etc. How many choices do we have for the first digit?

If we choose $digits[i]$ as the first digit, then none of $digits[1..i-1]$ can be used as digits anymore. Also keep in mind that we **must** choose $m$ digits.

Well, there are some digits that we cannot choose, namely $digits[m-n+2..n]$. If we choose any of them as the first digit, there will be $m-2$ digits left to choose from and we will have to make $m-1$ choices, which is impossible. We will call elements of $digits$ viable, if they don't lock us out from making choices later down the line.

Notice that we want the first digit in the resulting number to be as large as possible while also being viable. This is because between two numbers the most significant digit determines which number is greater (lexicographic ordering). What if there are multiple viable maximums in the candidate space? Well, then we choose the leftmost one, because that leaves the most options for the other digits.

One (systematic) way to do this would be to precompute a modified Range Minimum Query data structure. If we do it optimally, we can solve the given problem in $O(n)$ time and with $O(n)$ space. [Range minimum query - Wikipedia](https://en.wikipedia.org/wiki/Range_minimum_query)

Another way to look at this problem is to see that it's very similar to finding a sliding window maximum with variable length, which we know can be solved in linear time using a monotonic stack.

At first the sliding window is $digits[1..m-n+1]$. After choosing the i-th digit (the leftmost largest number in this window), the new sliding will become $digits[i+1,m-n+2]$. The right boundary always moves by one, as this corresponds to having one less digit left to choose. We can use a deque sorted in decreasing order (monotonic stack). Each element will be pushed an popped at most once.




### Burtsing balloons
You are given `n` balloons, indexed from `0` to `n - 1`. Each balloon is painted with a number on it represented by an array `nums`. You are asked to burst all the balloons.

If you burst the `ith` balloon, you will get `nums[i - 1] * nums[i] * nums[i + 1]` coins. If `i - 1` or `i + 1` goes out of bounds of the array, then treat it as if there is a balloon with a `1` painted on it.

Return _the maximum coins you can collect by bursting the balloons wisely_.

**Example 1:**

**Input:** nums = [3,1,5,8]
**Output:** 167
**Explanation:**
nums = [3,1,5,8] --> [3,5,8] --> [3,8] --> [8] --> []
coins =  3*1*5    +   3*5*8   +  1*3*8  + 1*8*1 = 167

**Example 2:**

**Input:** nums = [1,5]
**Output:** 10

**Constraints:**

-   `n == nums.length`
-   `1 <= n <= 300`
-   `0 <= nums[i] <= 100`


### Brute-force Solution:

Whenever a problem asks us to maximize something, there can be a few cases. One scenario is that the best solution is to just brute-force the problem. Make every possible choice for the first baloon to pop, then the second baloon, etc. and return the maximum of all possibilities. The amount of steps here would be at least $n!$.

Another scenario is that the problem exhibits some kind of greedy structure, wherein making locally optimal choices leads to a globally optimal solution. This is clearly not the case here if we just look at the array [100,1,100] :
$$(1 * 100 * 1)+(1 * 100 * 1)+(1 * 1 * 1)\lt(100 * 1 * 100)+(1 * 100 * 100)+(1 * 100 * 1)$$

Sometimes, we can optimize brute-force solutions by caching the solutions to its sub-problems. However, if you try to optimize the solution above, you will see that it's very hard to do so. The subproblems will be subsequences of the original array, because we don't know in what order the baloons will be popped when simulating the process. In fact, we will have to look at every possible ordering! For each subproblem, the value also depends on what previous baloons we popped, because the left and right boundaries will be different. We would need to query subproblem solutions by providing both the subsequences **and** their possible boundaries. We would have to hash the subsequences, which will be a pain. The number of subproblems would be in the order of the number of subsequences of the original array. This is the same as the number of subsets of a set of cardinality n, which is $2^n$. Clearly this solution will be hard to code and slow (but still faster than the brute force approach).

### A shift in perspective

There is a better way to do this. Instead of thinking about the problem moving forward in time, we can think backwards. Let's say $nums[i]$ is the **last** balloon we pop. We know what the left and right boundaries will be when this pop happens, because all the other balloons will be gone, regardless of the order they were popped in! This helps us avoid caching all the possible boundaries and just focus on one. For the whole array, the boundaries will be 1 and 1, because they are out of bounds.
![full_arr](notes/Algorithms/Bursting%20Balloons/full_arr.svg)

But since we don't know which balloon we should save for last, we need to try all of them. And for each of those cases, we need to try every possible way to pop the second-to-last balloon and etc. We will then take the maximum of all of these possibilities! Now the recursive structure of this solution is becoming clear, and we notice that we are doing repeated work.

We can cache the solutions to the subproblems (subarrays) by using the left and right indices of the subarrays as keys in a table. The value will be the solution for the given array.

Now let's look at a general subproblem. Let's say we want to know $maxCoins(nums[L,R])$. Let $i\in[L..R]$, again, be the last baloon popped in the subarray $[L..R]$.

![subproblem](notes/Algorithms/Bursting%20Balloons/subproblem.svg)

We see that the left and right boundaries of the left subarray will be $nums[L-1]$ and $nums[i]$ respectively. The left and right boundaries of the right subarray will be $nums[i]$ and $nums[R+1]$. And, of course, we finally pop $nums[i]$ and get $nums[L-1]*nums[i]*nums[R+1]$ amount of coins. 

Again, the most important point of this process is that **the boundaries do not change while we pop the baloons.** Because of this, both of the subarrays can be sure what the boundaries are at all times without worrying about them changing.

So the final recursive formula becomes the sum of the coins made from the left subarray, the right subarray, and popping the i-th balloon. And so, we take the maximum of all possible values of $i$:

$$maxCoins(nums[L..R]) = Max_{i=L}^{i=R}(nums[L-1]*nums[i]*nums[R+1] + maxCoins(nums[L..i-1]) + maxCoins(nums[i+1..R]))$$

$$nums[i..j] == 0, i \gt j$$

Because we are keeping the boundaries constant while solving subproblems, going out of bounds does not scare us. By the way, the last equation we wrote is just to make the recursive formula more readable by taking care of edge cases.

We also have the following base case:
$$maxCoins(i,i) == nums[i-1]*nums[i]*nums[i+1]$$

The top-down memoized solution is now clear. The only difficulty would be to hash pairs of integers in C++ to use as keys in the hash-map. We can circumvent this by creating a bottom-up solution that doesn't use recursion, which reduces the memory overhead. In most cases, such iterative solutions are faster than memoized recursive programs. However, there are cases when we might fill the dynamic programming table in a non-optimal order that will take more operations than a recursive solution would, because a recursive algorithm, by definition, will only calculate those cells that it actually needs to solve the problem. We will talk about this in more detail at a later time.

![dp-table](notes/Algorithms/Bursting%20Balloons/dp-table.svg)

(The cells below the diagonal are filled with 0s).

We can see from the above picture that to calculate $dp[L][R]$, we need to sum up corresponding pairs (balls of same color) plus $nums[i-1]*nums[i]*nums[j+1]$ and take the maximum. That value will go into the "white ball", which corresponds to $dp[L][R]$.

To get to the target, we need to know all the values of row 0 and column n-1. Those values themselves clearly require us to calculate all the values in the whole table. Because of this, we cannot skip any work. For this problem, the bottom-up solution does the exact same work as the top-down one, so the iterative algorithm should be better in every way.

At first we start with the red balls (the base cases). We can calculate the green cells straight away. After those, we start calculating the blue cells and etc. until we get to the target. Calculating the value of each cell takes $O(n)$ time, there are $O(n^2)$ number of cells, and so the time complexity of this solution is $O(n^3)$. The space taken up by the matrix will be $O(n^2)$. Coding this solution is trivial.

### Parallel computations

Another thing to think about is how we would make this solution faster by using multithreading. We could calculate the value of each cell on the diagonal at the same time, because all the values on the same diagonal are independent. This would reduce the time complexity of the algorithm to $O(n^2)$.

At first we might think that we can further optimize the solution by only storing one diagonal at a time, and changing up the values of this diagonal so that we can instantly calculate the values of the next one, but there is no obvious way to do this in this problem. I can't prove that we can't do it either, but it seems to me that it should be impossible from an information-theoretic point of view.

### Further practice problems

1.) Print the order in which we popped the balloons to get our optimal solution

2.) How many different ways can we pop the balloons? (permutations)

3.) Count how many ways we can get the maximum profit by popping the balloons in different order.

4.) Print the order of pops from the previous question

To help us solve some of these problems, notice that, when we popped the i-th balloon, we got two subarrays, one on the left and another on the right. Say we have a list of pop operations given to us that happened after the first pop: $L_1,L_2,R_1,L_3,R_2$.  The L-pops correspond to pop operations in the left subarray and R-pops in the right. Notice that if we change the relative ordering among the left pops, we will get a different answer in the left subproblem! The order in which we pop the baloons matters. Same goes for the right pops. 

However, keeping the relative order intact among the pops in each of the halves and changing when we do the left pops as compared to the right pops does not change the final answer, because operations in one half does not affect another! So the above sequence of pops is equivalent to, for example, $L_1,L_2,L_3,R_1,R_2$.
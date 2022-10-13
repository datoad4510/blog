## Next greater permutation

We are given a set $S$ with a strict ordering on its items. In programming terms, this could be a hashmap, where each item is associated with an integer. These integers tell us where in the ordering the items go. We then create an array using the elements of $S$. Such arrays can be compared to each other using a so called "lexicographical ordering". Basically, the elements starting from the left of the array ending at the right go from most significant to least significant. The first element that is different between the two arrays starting from the left determines which of the two is greater. This is how programming languages usually compare strings.

Example:

Say we are given $S=\{'a':1,'b':2,...,'z':26\}$. As an example, $'b' \lt 'z'$, because $2 \lt 26$.

We are also given the array $A=['a','b','a']$. We want to find the next lexicographically greater permutation of $A$. The ordering of all the permutations will be: $aab,aba,baa$. We see that $baa$ comes after $aba$, so we return it.

Another example:

$S=\{1:1,2:2,3:3,...\}$. This infinite set is isomorphic to the set of natural numbers, so we don't really need a hashmap for this - we already know how they are ordered.

$A=['2','1','3']$. The permutations are: $123,132,213,231,312,321$. We return $231$.

For the given question, the set $S$ is given implicitly. Assume that we already have a custom comparator for all the objects in the array $A$, which corresponds to having an $S$ provided to you.

## Solution

#### Rephrasing the problem

Say we are given an array $A$ where the elements have an ordering on them, with possible duplicates. We can create a really simple isomorphism (a bijection that preserves ordering) between the elements in $A$ and a subset of the natural numbers. Basically, the smallest element of $A$ will correspond to $1$, the second (strictly) smallest will correspond to $2$ and so on. As an example, $['d','d','z','g']$ will correspond to $[1,1,3,2]$. So, it's enough to solve the problem where we have the first however many natural numbers (with possible duplicates) in an array, and then trivially translate the solution back to the general case with a custom comparator. To make the problem even easier, we notice that a decimal number is less than another if and only if the array consisting of its digits is lexicographically smaller than the array corresponding to the other one. So, let's think about the permutations of the digits of a decimal number, and then simply extend the same logic to the case when each digit "can be greater than 9". So, instead of permuting $[1,1,3,2]$, we will think about permutations of digits in $1132$.

${\color{orange}\text{You might want to just skip forward and look at the optimal solution, because it's pretty intuitive.}}$

#### Brute-force #1

The easiest brute-force solution that we can come up with, is to generate the permutations of the number in any order and only keep the ones that are greater than the provided one. As we do this, we can also keep a running minimum among these greater permutations and update it as needed. There are $n!$ permutations in total. Comparing two permutations takes $O(n)$ time. So, in the worst case, the time complexity of this solution would be $O(n*n!)$. This algorithm is very slow.

Say we are starting with 123456789. It's obvious to us that the next greater permutation will be 123456798, but for the algorithm, this corresponds to the worst possible case time-wise. Literally every permutation of the number other than the initial one is greater than it. So, it will have to be compared with each of them! This seems like an overkill when we just changed the last two digits...

So what do we do now? Well, we could go over every permutation of the digits in an increasing order and just return the one that comes after the one we were asked. This would decrease the time complexity to $O(n!)$. But, how do we iterate over the permutations in an increasing order in the first place?

#### A vague idea (you can skip this)

Well, notice that if we start with the smallest permutation, what we want to do is to increase it as little as possible to get the next greater one. How do we do this? It seems like we somehow want to increase the least significant digit.

This is, for example, how addition works. The next greater number after $103$ is $104$. We increased the least significant digit. If the number has a $9$ at the end, we increase the one before it and make all the digits after the one we increased as small as possible (which is zero, in the case of addition). $1{\color{red}8}{\color{cyan}99} + 1=1{\color{red}9}{\color{cyan}00}$. Why is it so easy in the case of addition? It's basically because we are not constrained by the digits in the number. We don't have to swap them around, we can just replace the digits with anything anytime we want (this is how we would solve "next greater permutation with repetitions"). Because of this, we can't just greedily increase the last digit in the original problem. So what can we do then? We can only change the original number by rearranging its digits.

What we really want to do is to change its value as little as possible. If we swap a digit with a bigger one from the right, the number will increase. Since we want to increase the number as little as possible, let's swap the digit with the smallest bigger digit to the right. But what if we have two duplicates of such a digit? Well, then we would want to swap with the leftmost duplicate, as that will make the new number smaller. So let's look for the least significant digit that can be increased. We could look for it by finding the longest decreasing suffix. The element before the suffix could then be increased. After doing the swap, we can sort the suffix in increasing order to make the number as small as possible.

The logic above seems shaky. It's hard to see if we made any hidden assumptions, and proving the correctness of the algorithm seems hard. How do we know that we cannot find a permutation that is smaller than the one we would find using the algorithm above?  For example, what keeps us from swapping around 2 or 3 pairs? If we are limited to a single swap, the logic makes sense. But we would need to prove that doing more than one swap would be sub-optimal. Because of this, we will look for an alternative solution.

#### Brute force #2

What if we start thinking in reverse? When we output the permutations in an increasing order, all the numbers starting with 1 are smaller than all the numbers starting with 2 and so on. So, we want to go over the permutations in the following order: 1----, 2----, 3---- etc. But, how do we order the permutations starting with 1 among themselves? Well, we repeat the same thought process. Now the second digit decides which one among them is greater. So we order them in the following way: 12----, 13----, 14----- etc.

Notice that when we have the first few digits fixed, if we lexicographically order the digits to the right of them and concatenate them with the fixed digits, we get all the numbers whose first few digits are the fixed digits in sorted order! So, swap the running digit with the smallest digit to the right and fix it. Then recurse into and traverse the right sub-number in increasing order. Then increase the fixed number by swapping it with smallest bigger digit to the right (we can't always just swap it with the running number + 1, as some of the subproblems might have "gaps" between the digits). Keep doing this until we cannot find bigger digits.

We notice that having duplicate digits introduces some difficulties. What if there are two equal smallest digits? Well, then we want to swap with the leftmost one, because this will decrease a more significant digit, thus making the number even smaller, but still keeping it bigger than the initial one. This gives us an idea for a recursive solution!

At each step of the algorithm, we first recurse to the right, processing the right sub-number from the smallest permutation to the largest. We swap the current digit with the smallest one to its right that is still greater than it. If there are multiple such smallest numbers, we swap with the leftmost one.

It's not too hard to prove the correctness of this algorithm using induction on the length of a number.

```

// traverses the sub-number number[i..n] from smallest to largest

traverseInIncreasingOrder(number[i..n]):
	if(i == n):
		// print the number
		return

	// print the number

	smallestBiggerLeftmost = i
	while(smallestBiggerLeftmost != -1):
		swap(number[i],smallestBiggerLeftmost)
		traverseInIncreasingOrder(number[i+1..n])
		smallestBiggerLeftmost = findSmallestBiggerLeftmost(nums[i],number[i+1..n])
		

```


#### The optimal solution (combinatorially)

When we are constructing a permutation of a number, we can think of choosing which digit goes first in the permutation, then choose the second digit from what's left and so on. This process looks like a decision tree. After we've ran out of digits, we've arrived at some permutation. The permutations will be the leaves of this tree. 

Additionally, if we order the children of a node (order the decisions) in an increasing order (for example, going left corresponds to adding a 1, going right means adding a 2), then our tree will have the nice property, that if one sibling node is to the left of another, then all the permutations reachable by going down from the first node will be less than those reachable from the second one (all the numbers in the nodes will be in increasing order in each level of the tree). This is very easy to prove if we consider the significance of the digits being affected when going down. As a result, all the leaves will also be in sorted order going left to right, since they're all on the same level.

What we want to see, is how to move from a leaf to the one that is immediately to the right of it. We can just move through the tree and see what exactly happens to the numbers in the nodes.

Let's do just that. Say we start with the number 1223 and see how our algorithm traverses the decision tree:

![decision-tree](blog-website/notes/Algorithms/Next%20greater%20permutation/decision-tree.svg)

First of all, we notice that we have duplicate leaves. To get rid of them, we remove duplicate subtrees. This is equivalent to skipping subtrees whose roots have sibling nodes with the same value.

![removed-duplicates](blog-website/notes/Algorithms/Next%20greater%20permutation/removed-duplicates.svg)

Now we just take any leaf, and try moving to the leaft to the right of it.  Say we are moving from 2231 to 2312.

![path1](blog-website/notes/Algorithms/Next%20greater%20permutation/path1.svg)

We can see that we move up until we can move right by one. If the right node is a duplicate of the current one, we need to skip over it. Then, we keep moving left as far as we can go.

What does this mean in terms of the number? 

1. Moving up corresponds to popping an element off from the right side of the number.

2. If we can't move right, it means that we've already exhausted all the suffix possibilites (from smallest to largest) with a given prefix. Hence, the popped suffix is in a reverse sorted order.

3. If we can move right, that means that there exist bigger numbers in the suffix than the one we popped last while coming back up to this node. 

4. Moving right by one corresponds to choosing the smallest out of these bigger numbers. 

5. Skipping over duplicates means that the next number has to be strictly bigger. 

6. Moving left all the way means appending the suffix in a sorted order. Since the suffix was in a reverse-sorted order, we can just reverse it to sort it.

*Note: We don't actually need to "pop" anything. We can just use pointers to traverse the string.*

#### Optimal solution (algorithmically)

If your algorithmic skills are better than your combinatorial intuition, the following analogous view of the optimal solution might be more digestible.

The easiest way to come up with the optimal algorithm would be to first not worry about swaps, but instead, to simply construct every permutation from the available digits. We sort the digits first and add them to the running permutation greedily, always choosing the smallest one, unless we've already come across this digit while exhausting all the possibilites during a recursion step to avoid duplicates. This ensures that we print the numbers from smallest to largest.

```
sort(digits)

permutations(remaining_digits, running_permutation = ""):
	if(remaining_digits.empty()):
		print(running_permutation)

	previous_running_permutation = running_permutation

	for(i = 0; i < remaining_digits.size(); ++i):
		new_remaining_digits = remaining_digits.remove(i)
		new_running_permutation = running_permutation + remaining_digits[i]

		// avoid duplicates	
		if(new_running_permutation == previous_running_permutation)
			continue

		previous_running_permutation = new_running_permutation
		
		permutations(new_remaining_digits,new_running_permutation)

```

Why does this work? If we look at the code, we can see that first the numbers with the smallest significant digit get printed, and only after that we print the others. The same goes for all the nested calls. We are also skipping the recursion if after moving to the next possible remaining digit our new running permutation equals the previous running permutation. Doing so avoids duplicates. It's very easy to write an inductive proof for this.

We can then use a general problem-solving idea: Whenever we have a recursive algorithm that processes some search space in a specific order, and we want to know what will be processed after a given element of the search space, we can look at how the algorithm traverses the decision space by looking through the call stack to come up with an iterative step. This is, for example, how we find the successor or a predecessor of a node in a binary search tree. 

We will see that the call stack of this algorithm is equivalent to the previous approach. The algorithm basically constructs the tree from the previous heading. After following the call stack, we will arrive at the same optimal solution.
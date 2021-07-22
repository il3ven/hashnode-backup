## Pythagorean triplet in an array - Interview Question

In the problem of Pythagorean triplet we are given an array of numbers.
```
array = [1, 2, 3, 4, 5]
```
We have to return **true** if there exists a triplet of **(a, b, c)** such that **a^2^ + b^2^ = c^2^**. 

For example, in the above array the triplet (3, 4, 5) satisfies the condition.
```
3^2 + 4^2 = 5^2
9   + 16  = 25
```

## Solution
There are three different methods to solve the problem Pythagorean triplet in an array.

1. **Naive**

    Use three loop to check all triplets. Time Complexity - `O(n^3)`.

2. **Sort the Array**

    First sort the array. Fix one number out of the three triplet. Let it be `a`. Find `b` and `c` in O(n).  Total time complexity - `O(n^2)`

3. **Hashing**

    First create a [hashmap](https://en.wikipedia.org/wiki/Hash_table) of the array such that finding if an element exists an array takes O(1) time.  Then iterate over all pairs (a and b). For each pair check if `sqrt(a^2 + b^2)` exists in the array using the hashmap. If it exists then we have found a triplet. Time complexity - `O(n^2)`

Read below to find more detail on each solution to solve the Pythagorean Triplet problem.

## Simplifying the problem
Our task is to find if a triplet `(a, b, c)` exists such that `c^2 = a^2 + b^2`. If we square each element of  the given array then the problem simplifies to finding a triplet `(a, b, c)` in the new squared array such that `c = a + b`.

Example
```
array = [1, 2, 3, 4, 5]
squaredArray = [1, 3, 9, 16, 25]
```
The problem simplifies to find a triplet `(a, b, c)` from *squaredArray* such that `c = a + b`. The triplet in this example that satisfies the condition is `(9, 16, 25)` because `25 = 16 + 9`.

## Naive Method
Find all the possible triplets in the array and for each triplet check if it satisfies the condition.

```python
def pythagoreanTriplet(arr):
    squaredArray = [x ** 2 for x in arr]

    for i in range(len(squaredArray)):
        for j in range(i + 1, len(squaredArray)):
            for k in range(j + 1, len(squaredArray)):
                if (
                    squaredArray[i] == squaredArray[j] + squaredArray[k]
                    or squaredArray[j] == squaredArray[i] + squaredArray[k]
                    or squaredArray[k] == squaredArray[i] + squaredArray[j]
                ):
                    return True

    return False
```

#### Time Complexity to find Pythagorean Triplet in an Array using Naive Method
We are using three loops to check all the triplets. Hence, the time complexity is `O(n^3)` where n is the size of the array. The space complexity is `O(1)` as no extra space is used.

## Sorting the Array
### Intuition
The triplet that will satisfy the condition `c = a + b` will also satisfy the condition `a <= c` and `b <= c`. For an number `c` we only need to consider pairs `(a, b)` that are less than `c`.

### Algorithm
1. Square each element of the array.
2. Sort the array in ascending order in `O(n*logn)` time.
3. Pick `c` to be an element from the sorted array.
4. Pick `a` to be the first element of the array. Pick `b` to be element just left of `c`.

    **Reason:** As established in the intuition a and b should be less than c and all elements less that c are to its left.
5. If `a + b > c` then move *b* to it's left.
6. If `a + b < c` then move *a* to it's right. 

    (Note the invariant here. We only move a to the right and b to the left. This approach is known as the two pointer approach.)

    **Reason**: If `a + b > c` then we need to reduce `a + b` which can only be done by moving a or b to the left. If we move a to the left we will be able to see new pairs but the sum of those pairs will always be less than c. This is because we have already considered the pair of elements left to a with the elements to the right of b. Since, the sum was less than c we moved a to the right. The only choice we are left with is to move a to the right. Similarly, the argument applies for `a + b < c.` We move b to the right.
7. If for the current c there is no pair `(a, b)` such that `c = a + b` then we pick a new c from the sorted array.
8. Keep picking new c until every element has been picked or we find the triplet.

In the above solution to the Pythagorean triplet problem we can start from c as the rightmost/largest element of the sorted array. This ensures that we have a large subset of the array in the first iteration from which we can find a and b.  Further we can keep moving c to the left.

#### Time Complexity to find Pythagorean Triplet in an Array using Sorting
Total time Complexity is `O(n^2)`.

Steps 4, 5 and 6 take `O(n)` time. We traverse each element at most once. Keep in mind the invariant that a only moves right and b only moves left.

Steps 4, 5 and 6 can be repeated for at most `O(n)` time for different c. Hence the total time complexity is `O(n^2)`

### Animation
Given array = `[1, 4, 8, 10, 15, 16, 17]`, the Pythagorean triplet is `(8, 15, 17)`

![animation-sorting](https://i.imgur.com/vDMOnNs.gif)

### Code
```python
def pythagoreanTriplet(arr):
    squaredArray = [x ** 2 for x in arr]
    squaredArray.sort()
    
    for c in range(len(squaredArray)-1, 0, -1):
    	# find a and b such that squaredArray[c] = squaredArray[a] + squaredArray[b]
    	a = 0
    	b = c - 1
    	
    	while(a != b):
    		if(squaredArray[a] + squaredArray[b] == squaredArray[c]):
    			return True
    		
            # refer to step 5 in the algorithm
    		if(squaredArray[a] + squaredArray[b] > squaredArray[c]):
    			b -= 1
    			
            # refer to step 6 in the algorithm
    		elif(squaredArray[a] + squaredArray[b] < squaredArray[c]):
    			a += 1
    			
    			
    return False
```


## Hashing
### Creating an hashmap
A hashmap is an *undordered_set* in C, a *hasSet* in Java and a *dictionary* in Python. It is a data structure that allows us to insert elements in `O(1)` time. We can also query in `O(1)` time if the hashmap contains the given element.

### Intuition
Once we have a hashmap of our squared array (see simplifying the problem) we can pick `(a, b)` and ask the question if `a + b` is present in the array or not. This question can be answered in `O(1)` time using the hashmap. We can ask the question for all the possible pairs in `O(n^2)` time.

### Algorithm
1. Square each element of the array.
2. Create a hashmap of the array.
3. Loop through all the pairs `(a + b)` using two loops and check if `a + b` is present in the array or not using the hashmap.
4. If present return `True` else if no pair found return `False`

#### Time Complexity to find Pythagorean Triplet in an Array using Hashing
Total Time Complexity: `O(n^2)`

Time complexity for steps 1 and 2 `O(n)`. Time complexity to loop through all pairs using two loops is `O(n^2)`.

### Animation
Given array = `[1, 4, 8, 10, 15, 16, 17]`, the Pythagorean triplet is `(8, 15, 17)`

![animation-hashing](https://i.imgur.com/cSfbNUk.gif)

### Code
```python
from collections import defaultdict

def createHashmap(arr):
	hashmap = defaultdict(list)
	for elm in arr:
		hashmap[elm] = hashmap[elm] if elm in hashmap else 1
		
	return hashmap

def pythagoreanTriplet(arr):
	squaredArray = [x ** 2 for x in arr]
	hashmap = createHashmap(squaredArray)
	
	for a in range(len(squaredArray)):
		for b in range(a+1, len(squaredArray)):
			if(squaredArray[a] + squaredArray[b] in hashmap):
				return True
	
	return False
```
# Java Time Complexity: Beginner to Interview Level Notes

Welcome to your ultimate guide on mastering time complexity in Java. Whether you are writing your first loop or preparing for technical interviews at top tech companies, this guide breaks down every concept using simple English, clear step-by-step reasoning, and practical Java code examples.

---

## Table of Contents
1. [What is Time Complexity?](#1-what-is-time-complexity)
2. [What are $n$ and Input Size?](#2-what-are-n-and-input-size)
3. [Big O, Big Omega, and Big Theta](#3-big-o-big-omega-and-big-theta)
4. [All Common Complexities ($O(1)$ to $O(n!)$)](#4-all-common-complexities)
5. [How to Calculate Time Complexity Step by Step](#5-how-to-calculate-time-complexity-step-by-step)
6. [Rules for Simplifying Complexity](#6-rules-for-simplifying-complexity)
7. [Time Complexity of Different Java Loop Patterns](#7-time-complexity-of-different-java-loop-patterns)
8. [Nested Loops with Different Conditions and Boundaries](#8-nested-loops-with-different-conditions-and-boundaries)
9. [Loops that Double or Halve their Variables](#9-loops-that-double-or-halve-their-variables)
10. [Linear Search vs Binary Search](#10-linear-search-vs-binary-search)
11. [Sorting Algorithms Time Complexity](#11-sorting-algorithms-time-complexity)
12. [Time Complexity of Java Arrays and Strings](#12-time-complexity-of-java-arrays-and-strings)
13. [Java Collections Complexity Cheat Sheet](#13-java-collections-complexity-cheat-sheet)
14. [Two Pointers and Sliding Window Patterns](#14-two-pointers-and-sliding-window-patterns)
15. [Recursion and Recursive Time Complexity](#15-recursion-and-recursive-time-complexity)
16. [Time Complexity vs Space Complexity](#16-time-complexity-vs-space-complexity)
17. [Best-Case, Average-Case, and Worst-Case Analysis](#17-best-case-average-case-and-worst-case-analysis)
18. [Common Time Complexity Mistakes Made by Beginners](#18-common-time-complexity-mistakes-made-by-beginners)
19. [Interview Questions with Step-by-Step Explanations](#19-interview-questions-with-step-by-step-explanations)
20. [Practice Section (25 Questions)](#20-practice-section-25-questions)
21. [Practice Section Answers & Explanations](#21-practice-section-answers--explanations)

---

## 1. What is Time Complexity?

Time complexity is a theoretical concept used in computer science to measure **how the execution time of an algorithm grows as the size of the input grows**.

- **Why not use a stopwatch?** If you measure execution time using `System.currentTimeMillis()` in Java, the results will vary depending on your computer's processor, RAM, background apps, and the Java Virtual Machine (JVM) warmup/garbage collection. 
- **What is the solution?** Instead of measuring seconds, we count the exact number of basic operations (like comparisons, arithmetic assignments, or loop iterations) as a mathematical function of the input size.

---

## 2. What are $n$ and Input Size?

In algorithm analysis, **$n$** represents the **input size**. 

Depending on the problem type, $n$ can mean different things:
- For an array or list, $n$ is the **number of elements** in the array (`arr.length`).
- For a string, $n$ is the **length of the string** (`str.length()`).
- For a number, $n$ is the **value of the number** itself (e.g., finding factors of $n$).
- For a matrix, $n$ and $m$ represent rows and columns.

---

## 3. Big O, Big Omega, and Big Theta

Asymptotic notations describe how a function behaves as $n$ grows very large.

| Notation | Symbol | Meaning in Plain English | Analogy |
| :--- | :--- | :--- | :--- |
| **Big O** | $O$ | **Upper Bound** (Worst-case scenario). The algorithm will *never* take longer than this rate. | "I can finish this task in *at most* 1 hour." |
| **Big Omega** | $\Omega$ | **Lower Bound** (Best-case scenario). The algorithm will *at least* take this long. | "This task will take *at least* 10 minutes." |
| **Big Theta** | $\Theta$ | **Tight Bound** (Average/Exact growth rate). The upper and lower bounds match. | "This task will take *precisely* 30 minutes." |

> **Interview Tip:** In 99% of technical interviews, when people ask for "Time Complexity", they are asking for **Big O notation ($O$)** because engineers want to guarantee that code won't freeze under heavy load.

---

## 4. All Common Complexities

Here are the standard time complexities ordered from **fastest (best)** to **slowest (worst)**:

| Notation | Name | Example Scenario |
| :--- | :--- | :--- |
| $O(1)$ | Constant Time | Accessing an array element by index: `arr[5]` |
| $O(\log n)$ | Logarithmic Time | Binary Search in a sorted array |
| $O(n)$ | Linear Time | Scanning an unsorted array in a single loop |
| $O(n \log n)$ | Linearithmic Time | Efficient sorting algorithms (Merge Sort, Heap Sort) |
| $O(n^2)$ | Quadratic Time | Nested loops comparing every element with every other element |
| $O(n^3)$ | Cubic Time | Triple nested loops (e.g., naive matrix multiplication) |
| $O(2^n)$ | Exponential Time | Recursive Fibonacci, generating all subsets (power set) |
| $O(n!)$ | Factorial Time | Traveling Salesperson Problem brute-force permutations |

### Visual Graph of Growth Rates
```text
Performance (Time taken)
  ^
  |                                                 / O(n!)
  |                                              / O(2^n)
  |                                          / O(n^3)
  |                                      / O(n^2)
  |                                  / O(n log n)
  |                              / O(n)
  |                          / O(log n)
  |                      / O(1)
  +----------------------------------------------------> Input Size (n)
```

---

## 5. How to Calculate Time Complexity Step by Step

To evaluate any code snippet, apply this structured 6-step framework:
1. **Java Code:** Read the code snippet carefully.
2. **Step 1: Identify the input size ($n$):** Determine what variable represents the workload.
3. **Step 2: Count the operations:** Count how many times each statement or loop body executes relative to $n$.
4. **Step 3: Write the mathematical expression:** Combine counts into a total operations formula $T(n)$.
5. **Step 4: Simplify the expression:** Apply asymptotic rules (drop constants and lower-order terms).
6. **Final Answer:** State the Big O complexity and provide clear reasoning.

---

## 6. Rules for Simplifying Complexity

When analyzing expressions like $T(n) = 3n^2 + 5n + 10$, follow these rules to arrive at the final Big O:

1. **Ignore Constants:** Drop coefficient multipliers. $O(5n)$ becomes $O(n)$, and $O(100n^2)$ becomes $O(n^2)$. *Reason:* As $n$ approaches infinity, constant factors become negligible.
2. **Remove Lower-Order Terms:** Keep only the fastest-growing term. In $O(n^2 + n + \log n)$, $n^2$ dominates, so the complexity simplifies to $O(n^2)$.
3. **Add Consecutive Loops:** If loop A runs $O(n)$ times and subsequent loop B runs $O(n)$ times, total time is $O(n + n) = O(2n) = O(n)$.
4. **Multiply Nested Loops:** If an outer loop runs $n$ times and an inner loop runs $n$ times *for each iteration*, total time is $O(n \times n) = O(n^2)$.

---

## 7. Time Complexity of Different Java Loop Patterns

### A. Single Loop ($O(n)$)
```java
public void printElements(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        System.out.println(arr[i]);
    }
}
```
- **Step 1:** Input size is $n = \text{arr.length}$.
- **Step 2:** The loop runs $n$ times. Inside the loop, printing takes $O(1)$ constant time.
- **Step 3:** Total operations = $n \times 1 = n$.
- **Step 4:** Simplify: $O(n)$.
- **Final Answer:** $O(n)$ Linear Time because the execution scales in direct proportion to the array length.

### B. Consecutive Loops ($O(n)$)
```java
public void dualLoops(int[] arr) {
    // Loop 1
    for (int i = 0; i < arr.length; i++) {
        System.out.println(arr[i]);
    }
    // Loop 2
    for (int j = 0; j < arr.length; j++) {
        System.out.println(arr[j] * 2);
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** Loop 1 runs $n$ times. Loop 2 runs $n$ times sequentially.
- **Step 3:** Total operations = $n + n = 2n$.
- **Step 4:** Drop constant 2: $O(n)$.
- **Final Answer:** $O(n)$ because consecutive loops add up, not multiply.

### C. Standard Nested Loops ($O(n^2)$)
```java
public void printMatrixPairs(int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            System.out.println(i + ", " + j);
        }
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** For every 1 iteration of the outer loop ($i$), the inner loop runs $n$ full iterations ($j$ goes from $0$ to $n-1$).
- **Step 3:** Total operations = $n \times n = n^2$.
- **Step 4:** Simplify: $O(n^2)$.
- **Final Answer:** $O(n^2)$ Quadratic Time.

### D. Nested Loops with Fixed Inner Bound ($O(n)$)
```java
public void fixedInnerLoop(int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < 5; j++) { // Fixed boundary!
            System.out.println(i + j);
        }
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** Outer loop runs $n$ times. Inner loop runs exactly $5$ times *every single time*, regardless of $n$.
- **Step 3:** Total operations = $n \times 5 = 5n$.
- **Step 4:** Drop constant 5: $O(n)$.
- **Final Answer:** $O(n)$. **Crucial Interview Note:** Do not assume all nested loops are $O(n^2)$. Always check if the inner loop boundary depends on $n$ or is a fixed constant.

---

## 8. Nested Loops with Different Conditions and Boundaries

### A. Dependent Inner Loop (Triangular Iteration) ($O(n^2)$)
```java
public void triangularLoop(int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j <= i; j++) {
            System.out.print("*");
        }
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** 
  - When $i = 0$, inner loop runs $1$ time.
  - When $i = 1$, inner loop runs $2$ times.
  - When $i = n-1$, inner loop runs $n$ times.
- **Step 3:** Total operations = $1 + 2 + 3 + \dots + n = \frac{n(n + 1)}{2} = \frac{n^2 + n}{2} = 0.5n^2 + 0.5n$.
- **Step 4:** Drop constants ($0.5$) and lower-order terms ($0.5n$): $O(n^2)$.
- **Final Answer:** $O(n^2)$. This represents a triangular execution space (like printing lower triangular matrices or checking all unique pairs).

---

## 9. Loops that Double or Halve their Variables

When loop variables are multiplied, divided, squared, or square-rooted, logarithmic time complexity $O(\log n)$ emerges.

### A. Halving Loop ($O(\log n)$)
```java
public void halvingLoop(int n) {
    for (int i = n; i > 0; i /= 2) {
        System.out.println(i);
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** Let's trace $n = 16$:
  - Iteration 1: $i = 16$
  - Iteration 2: $i = 8$
  - Iteration 3: $i = 4$
  - Iteration 4: $i = 2$
  - Iteration 5: $i = 1$
  - Total steps = $5$, which equals $\log_2(16) + 1$.
- **Step 3:** In general, $n$ is divided by $2$ $k$ times until it reaches $1$. $\frac{n}{2^k} = 1 \implies n = 2^k \implies k = \log_2(n)$.
- **Final Answer:** $O(\log n)$ Logarithmic Time because the remaining search space cuts in half every iteration.

### B. Doubling Loop ($O(\log n)$)
```java
public void doublingLoop(int n) {
    for (int i = 1; i < n; i *= 2) {
        System.out.println(i);
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** $i$ starts at $1$ and multiplies by $2$ each step ($1, 2, 4, 8, 16, \dots$).
- **Step 3:** It reaches $n$ in approximately $\log_2(n)$ steps.
- **Final Answer:** $O(\log n)$.

---

## 10. Linear Search vs Binary Search

| Feature | Linear Search | Binary Search |
| :--- | :--- | :--- |
| **Prerequisite** | None (works on unsorted data) | Array **must** be sorted |
| **How it works** | Checks elements one by one from left to right | Compares target with middle element and discards half |
| **Time Complexity** | $O(n)$ | $O(\log n)$ |
| **Java Example** | `Arrays.asList(arr).contains(x)` | `Arrays.binarySearch(arr, x)` |

---

## 11. Sorting Algorithms Time Complexity

| Algorithm | Best Case | Average Case | Worst Case | Space Complexity | Why Worst Case Happens |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bubble Sort** | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Unsorted array requiring full pairwise passes |
| **Selection Sort** | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Always scans remaining unsorted elements |
| **Insertion Sort** | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Reverse-sorted array requiring shifts |
| **Merge Sort** | $O(n \log n)$ | $O(n \log n)$ | $O(n \log n)$ | $O(n)$ | Always divides array in half and merges |
| **Heap Sort** | $O(n \log n)$ | $O(n \log n)$ | $O(n \log n)$ | $O(1)$ | Binary heap construction and extraction |
| **Quick Sort** | $O(n \log n)$ | $O(n \log n)$ | $O(n^2)$ | $O(\log n)$ | Poor pivot selection (already sorted array with naive pivot) |

---

## 12. Time Complexity of Java Arrays and Strings

Knowing built-in Java operations prevents accidental performance bottlenecks in coding interviews.

### Arrays (`int[]`, `String[]`)
- **Access by index (`arr[i]`):** $O(1)$
- **Update by index (`arr[i] = val`):** $O(1)$
- **Search (Unsorted):** $O(n)$
- **Search (Sorted via Binary Search):** $O(\log n)$
- **Insertion/Deletion (Middle of array):** $O(n)$ (requires shifting all subsequent elements)
- **Copying array (`Arrays.copyOf()`):** $O(n)$

### Strings (`String`, `StringBuilder`)
- **`str.length()`:** $O(1)$ (Length is pre-calculated and cached in Java Strings).
- **`str.charAt(i)`:** $O(1)$
- **`str.substring(i, j)`:** $O(k)$ where $k = j - i$ (creates a brand-new string copy).
- **`str.indexOf(sub)`:** $O(n \times m)$ where $n$ is string length and $m$ is substring length.
- **String Concatenation (`str1 + str2`):** $O(n + m)$ (creates new memory buffers).
- **`StringBuilder.append()`:** $O(1)$ amortized.

---

## 13. Java Collections Complexity Cheat Sheet

| Collection Class | Operation | Time Complexity | Notes |
| :--- | :--- | :--- | :--- |
| **ArrayList** | `add(val)` (at end) | $O(1)$ Amortized | Occasional $O(n)$ resize when array capacity doubles |
| **ArrayList** | `get(index)` | $O(1)$ | Direct pointer lookup |
| **ArrayList** | `add(index, val)` | $O(n)$ | Shifts elements to the right |
| **ArrayList** | `remove(index)` | $O(n)$ | Shifts elements to the left |
| **LinkedList** | `addFirst() / removeFirst()` | $O(1)$ | Direct head node manipulation |
| **LinkedList** | `get(index)` | $O(n)$ | Traverses nodes sequentially from head/tail |
| **HashSet** | `add() / contains() / remove()` | $O(1)$ Expected | Hash collisions can degrade worst-case to $O(n)$ |
| **HashMap** | `put() / get() / remove()` | $O(1)$ Expected | Based on bucket hashing and equals/hashCode |
| **TreeSet / TreeMap**| `add() / contains() / remove()` | $O(\log n)$ | Implemented via Red-Black Self-Balancing Binary Search Trees |
| **PriorityQueue** | `offer() / poll()` | $O(\log n)$ | Binary Heap sift-up / sift-down operations |
| **PriorityQueue** | `peek()` | $O(1)$ | Inspects the root element |
| **Collections.sort()** | Sorting Lists | $O(n \log n)$ | Uses Timsort algorithm (hybrid of Merge Sort and Insertion Sort) |

---

## 14. Two Pointers and Sliding Window Patterns

### A. Two Pointers Pattern ($O(n)$)
Used frequently on sorted arrays or strings (e.g., checking palindromes or finding two numbers that sum to a target).
```java
public boolean isPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) return false;
        left++;
        right--;
    }
    return true;
}
```
- **Analysis:** Each character is visited at most once by either `left` or `right` pointer.
- **Time Complexity:** $O(n)$ where $n$ is string length.

### B. Sliding Window Pattern ($O(n)$)
Used for subarrays or substrings of fixed or variable size.
```java
public int maxSumSubarray(int[] arr, int k) {
    int maxSum = 0, windowSum = 0;
    for (int i = 0; i < k; i++) {
        windowSum += arr[i]; // Build first window
    }
    maxSum = windowSum;
    for (int i = k; i < arr.length; i++) {
        windowSum += arr[i] - arr[i - k]; // Slide window
        maxSum = Math.max(maxSum, windowSum);
    }
    return maxSum;
}
```
- **Analysis:** We do an initial scan of size $k$ ($O(k)$), then slide the window across the remaining $n-k$ elements in a single pass ($O(n-k)$). Total time = $O(k + n - k) = O(n)$.
- **Time Complexity:** $O(n)$.

---

## 15. Recursion and Recursive Time Complexity

To find the time complexity of a recursive function, write down its **Recurrence Relation** (how time relates to smaller subproblems) and solve it using the recursion tree method or the Master Theorem.

### A. Single Recursive Call ($O(n)$)
```java
public int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** Each call to `factorial(n)` triggers 1 recursive call to `factorial(n-1)`.
- **Step 3:** Total depth of recursion stack = $n$ levels. Each level performs $O(1)$ work.
- **Final Answer:** $O(n)$ time and $O(n)$ space (due to call stack frames).

### B. Multiple Recursive Calls (Fibonacci) ($O(2^n)$)
```java
public int fib(int n) {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```
- **Analysis:** Every call branches into 2 sub-calls until base cases are met. This creates a binary recursion tree of depth $n$ with $2^0 + 2^1 + 2^2 + \dots + 2^n \approx 2^{n+1} - 1$ total nodes.
- **Final Answer:** $O(2^n)$ Exponential Time (very inefficient without memoization or dynamic programming).

### C. Divide and Conquer (Binary Search Recursion) ($O(\log n)$)
```java
public int binarySearchRec(int[] arr, int low, int high, int target) {
    if (low > high) return -1;
    int mid = low + (high - low) / 2;
    if (arr[mid] == target) return mid;
    if (arr[mid] > target) return binarySearchRec(arr, low, mid - 1, target);
    return binarySearchRec(arr, mid + 1, high, target);
}
```
- **Analysis:** The problem size is divided in half at each recursive level. The recurrence relation is $T(n) = T(n/2) + O(1)$.
- **Final Answer:** $O(\log n)$.

---

## 16. Time Complexity vs Space Complexity

| Metric | Definition | Common Causes in Java |
| :--- | :--- | :--- |
| **Time Complexity** | How fast an algorithm runs as input grows. | Loops, recursion depth, nested traversals. |
| **Space Complexity** | How much memory/RAM an algorithm consumes as input grows. | Creating new arrays, `ArrayList`, HashMaps, recursive call stack frames. |

> **Pro-Tip:** Often there is a trade-off between time and space. You can make an algorithm faster by caching results in memory (Memoization / Dynamic Programming), which uses more space to save time.

---

## 17. Best-Case, Average-Case, and Worst-Case Analysis

Let's examine **Linear Search** to understand all three cases:
```java
public int search(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            return i; // Found!
        }
    }
    return -1;
}
```
- **Best-Case ($O(1)$):** The target is located at the very first index (`arr[0]`). The loop terminates immediately after 1 check.
- **Average-Case ($O(n)$):** The target is located somewhere in the middle of the array on average.
- **Worst-Case ($O(n)$):** The target is at the very end of the array, or not present at all. We must check all $n$ elements.

---

## 18. Common Time Complexity Mistakes Made by Beginners

1. **Assuming all loops run $n$ times:** Forgetting to check if a loop runs up to a constant or logarithmic limit.
2. **Confusing Space and Time:** Stating that a recursive function with depth $n$ is $O(1)$ time because there's only one line of code.
3. **Ignoring String Immutability:** Forgetting that string concatenation in a loop (`str += c`) creates a new string object each time, turning an $O(n)$ loop into an $O(n^2)$ performance trap. Always use `StringBuilder`!
4. **Misunderstanding Collection Methods:** Assuming `ArrayList.contains()` is $O(1)$ when it is actually $O(n)$ because it scans sequentially.

---

## 19. Interview Questions with Step-by-Step Explanations

### Interview Question 1: What is the time complexity of this code?
```java
public void mystery(int n) {
    int count = 0;
    for (int i = 1; i <= n; i *= 2) {
        for (int j = 0; j < i; j++) {
            count++;
        }
    }
}
```
- **Step 1:** Input size is $n$.
- **Step 2:** Outer loop runs $k = \log_2(n)$ times, where $i$ takes values $1, 2, 4, 8, \dots, n$.
- **Step 3:** Inner loop runs $i$ times per outer iteration. Total operations = $1 + 2 + 4 + 8 + \dots + n$.
- **Step 4:** By geometric series summation, $1 + 2 + 4 + \dots + n = 2n - 1$.
- **Final Answer:** $O(n)$ Linear Time! Even though there are nested loops, the total inner iterations sum up linearly to $2n - 1$.

### Interview Question 2: What is the time complexity of printing all digits of a number?
```java
public void printDigits(int n) {
    while (n > 0) {
        int digit = n % 10;
        System.out.println(digit);
        n /= 10;
    }
}
```
- **Step 1:** Input size is the numerical value $n$.
- **Step 2:** In each iteration, $n$ is divided by $10$.
- **Step 3:** The number of digits in a number $n$ is proportional to $\log_{10}(n)$.
- **Final Answer:** $O(\log_{10} n)$, which simplifies in Big O notation to $O(\log n)$.

---

## 20. Practice Section (25 Questions)

Attempt the following 25 questions independently. Solutions and explanations follow in section 21.

1. **Q1:** What is the time complexity of accessing an element in an `int[]` array by its index?
2. **Q2:** Analyze this single loop:
   ```java
   for (int i = 0; i < n; i += 2) { System.out.println(i); }
   ```
3. **Q3:** Analyze two consecutive loops:
   ```java
   for (int i = 0; i < n; i++) { }
   for (int j = 0; j < 1000; j++) { }
   ```
4. **Q4:** Analyze nested loops where both depend on $n$:
   ```java
   for (int i = 0; i < n; i++) {
       for (int j = 0; j < n; j++) { }
   }
   ```
5. **Q5:** Analyze nested loops where inner loop depends on outer loop variable:
   ```java
   for (int i = 0; i < n; i++) {
       for (int j = i; j < n; j++) { }
   }
   ```
6. **Q6:** Analyze logarithmic halving loop:
   ```java
   for (int i = n; i > 1; i /= 3) { }
   ```
7. **Q7:** Analyze logarithmic squaring loop:
   ```java
   for (int i = 2; i < n; i = i * i) { }
   ```
8. **Q8:** What is the time complexity of `HashMap.get(key)` in the average case?
9. **Q9:** What is the time complexity of `HashSet.contains(val)` in the worst case (with severe hash collisions)?
10. **Q10:** Analyze this nested loop combination:
    ```java
    for (int i = 0; i < n; i++) {
        for (int j = 1; j < n; j *= 2) { }
    }
    ```
11. **Q11:** Analyze this independent nested loop combination:
    ```java
    for (int i = 0; i < n; i++) { }
    for (int j = 0; j < m; j++) { }
    ```
12. **Q12:** What is the time complexity of `Collections.sort()` on an `ArrayList` of size $n$?
13. **Q13:** What is the time complexity of inserting an element at index `0` of an `ArrayList` of size $n$?
14. **Q14:** What is the time complexity of inserting an element at the beginning of a `LinkedList` of size $n$?
15. **Q15:** Analyze binary search time complexity.
16. **Q16:** Analyze linear search time complexity.
17. **Q17:** Analyze Bubble Sort worst-case time complexity.
18. **Q18:** Analyze Merge Sort time complexity across all cases.
19. **Q19:** Analyze standard recursive Fibonacci function `fib(n)`.
20. **Q20:** Analyze tail-recursive factorial function.
21. **Q21:** Analyze this two-pointer array traversal:
    ```java
    int l = 0, r = arr.length - 1;
    while (l < r) { l++; r--; }
    ```
22. **Q22:** Analyze a fixed-size sliding window of size $k$ over an array of size $n$.
23. **Q23:** What is the time complexity of appending $n$ characters to a `StringBuilder`?
24. **Q24:** What is the time complexity of doing string concatenation `str += ch` inside a loop running $n$ times?
25. **Q25:** Analyze this triple nested loop:
    ```java
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            for (int k = 0; k < n; k++) { }
        }
    }
    ```

---

## 21. Practice Section Answers & Explanations

- **A1:** **$O(1)$** - Direct memory offset calculation allows instant access.
- **A2:** **$O(n)$** - Incrementing by 2 still scales linearly ($n/2$ iterations, constant factor dropped).
- **A3:** **$O(n)$** - Loop 1 is $O(n)$, Loop 2 is $O(1000)$ (constant). $O(n + 1000) = O(n)$.
- **A4:** **$O(n^2)$** - $n$ outer iterations multiplied by $n$ inner iterations.
- **A5:** **$O(n^2)$** - Triangular iterations summing to $\frac{n(n+1)}{2}$.
- **A6:** **$O(\log_3 n) = O(\log n)$** - Division by 3 every step creates logarithmic bounds.
- **A7:** **$O(\log \log n)$** - The exponent of the exponent grows doubly fast, leading to iterated logarithm.
- **A8:** **$O(1)$** - Expected constant time via hash bucket lookup.
- **A9:** **$O(n)$** - Worst-case hash collisions degrade bucket performance to linear traversal.
- **A10:** **$O(n \log n)$** - Outer loop runs $n$ times, inner loop runs $\log n$ times. $n \times \log n = O(n \log n)$.
- **A11:** **$O(n + m)$** - Two independent variables $n$ and $m$ cannot be combined into a single term without knowing their relationship.
- **A12:** **$O(n \log n)$** - Timsort guarantees $O(n \log n)$ sorting time.
- **A13:** **$O(n)$** - Inserting at index 0 requires shifting all $n$ existing elements to the right.
- **A14:** **$O(1)$** - Linked list head insertion only requires updating head and node pointers.
- **A15:** **$O(\log n)$** - Halves the search space on every comparison.
- **A16:** **$O(n)$** - Scans elements one by one.
- **A17:** **$O(n^2)$** - Complete nested comparisons in worst-case reverse order.
- **A18:** **$O(n \log n)$** - Consistent divide-and-conquer splitting and linear merging across all cases.
- **A19:** **$O(2^n)$** - Binary branching recursive tree.
- **A20:** **$O(n)$** - Linear recursion depth with single recursive calls.
- **A21:** **$O(n)$** - Pointers move towards each other, visiting each element at most once.
- **A22:** **$O(n)$** - Initial window setup plus single pass sliding across $n$ elements.
- **A23:** **$O(n)$** - `StringBuilder` uses amortized resizing; total time to append $n$ characters is linear.
- **A24:** **$O(n^2)$** - String immutability forces a brand-new memory allocation and copy of length $1, 2, 3, \dots, n$ on every iteration ($\frac{n(n+1)}{2}$).
- **A25:** **$O(n^3)$** - Three nested loops running $n$ times each ($n \times n \times n$).

---
*Happy coding and best of luck with your technical interviews!*

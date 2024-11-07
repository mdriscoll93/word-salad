---
description: >-
  learn how to compare arrays in Shell scripting. Arrays are useful data
  structures for storing multiple values, and comparing them is a common task in
  programming. You will work with three arrays and d
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# Array comparison

<pre class="language-bash"><code class="lang-bash"><strong>#!/bin/bash 
</strong>
a=(3 5 8 10 6)
b=(6 5 4 12)
c=(14 7 5 7)

# Initialize an array to store common elements between a and b
z=()

# Compare arrays a and b
for x in "${a[@]}"; do
  for y in "${b[@]}"; do
    if [ $x = $y ]; then
      z+=($x)
    fi
  done
done

echo "Common elements between a and b: ${z[@]}"
</code></pre>

Let's explain this code in detail:

* `z=()` Initializes an empty array `z` to store the common elements.
* `for x in "${a[@]}"` is a loop that iterates over each element in array `a`. The `"${a[@]}"` syntax expands to all elements of the array.
* We then have a nested loop `for y in "${b[@]}"` that iterates over each element in array `b`.
* `if [ $x = $y ]` checks if the current element from `a` is equal to the current element from `b`.
* If they are equal, we add this element to array `z` using `z+=($x)`.
* Finally, we print the common elements using `echo "Common elements between a and b: ${z[@]}"`. The `${z[@]}` syntax expands to all elements of array `z`.

```bash
#!/bin/bash 

a=(3 5 8 10 6)
b=(6 5 4 12)
c=(14 7 5 7)

# Initialize an array to store common elements between a and b
z=()

# Compare arrays a and b
for x in "${a[@]}"; do
  for y in "${b[@]}"; do
    if [ $x = $y ]; then
      z+=($x)
    fi
  done
done

echo "Common elements between a and b: ${z[@]}"

# Initialize an array to store common elements among a, b, and c
j=()

# Compare array c with the common elements found in z
for i in "${c[@]}"; do
  for k in "${z[@]}"; do
    if [ $i = $k ]; then
      j+=($i)
    fi
  done
done

echo "Common elements among a, b, and c: ${j[@]}"
```

This code is similar to the previous loop, but with a few key differences:

* We initialize a new empty array `j` to store the final common elements.
* The outer loop `for i in "${c[@]}"` iterates over elements in array `c`.
* The inner loop `for k in "${z[@]}"` iterates over the common elements we found between `a` and `b`, which are stored in array `z`.
* We compare elements from `c` with elements in `z`, and if there's a match, we add it to array `j`.
* Finally, we print the common elements among all three arrays.

### executing our script

```bash
~ $ ./array-comparison.sh
Common elements between a and b: 5 6
Common elements among a, b, and c: 5
```

This output shows that:

* The common elements between arrays `a` and `b` are 5 and 6.
* The common element among all three arrays (`a`, `b`, and `c`) is 5.

If you don't see this output or encounter an error, double-check your script for typos or missing parts.

## Summary <a href="#summary" id="summary"></a>

In this lab, you learned how to compare arrays in Shell scripting. You created a script that finds common elements among three arrays using nested loops and conditional statements. This exercise demonstrated key concepts in Shell scripting, including:

1. Creating and initializing arrays
2. Using nested loops to compare array elements
3. Conditional statements to check for equality
4. Adding elements to arrays dynamically
5. Making a script executable and running it

These skills are fundamental in Shell scripting and can be applied to more complex data processing tasks in the future. As you continue to work with Shell scripts, you'll find that array manipulation is a powerful tool for efficiently handling data sets.

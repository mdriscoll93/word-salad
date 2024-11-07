---
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

# Arrays

## Overview

In Bash, arrays are a type of data structure that lets you store multiple values in a single variable. Each element in the array can be accessed by its index, making it easier to work with data collections.

Let's briefly go over the basics of arrays in Bash...

### Declaring and using an array

There are two main ways to declare an array.&#x20;

1. You can make your declaration between parenthesis and whitespace-separated elements:

```bash
# Define an array
my_array=(element1 element2 element3)
```

2. you can use the `declare` Keyword to declare your array, with or without initializing:

```bash
declare -a my_array

# Then, we can add elements to our array by assigning a value to a specific index
my_array[0]="element1"
my_array[1]="element2"
my_array[2]="element3"
```

3. We can also append to an array without specifying an index:

```bash
my_array+=("date")
```

4. Accessing array elements

Use the index number in square brackets to access an element:

```bash
echo "${my_array[0]}"  # Outputs: element1
```

We can access all elements in the array with `[*]` or `[@]`:

```bash
echo "${my_array[@]}"  # Outputs: element1 element2 element3 date
echo "${my_array[*]}"  # Same output as above
```

5. Array length

To get the number of elements in an array, use the `#` operator:

```bash
echo "${#my_array[@]}"  # Outputs: 4
```

6. Looping through an array

We can loop through all the elements using a `for` loop:

```bash
for i in "{my_array[@]}"; do
    echo "$i"
done
```

7. Associative arrays

Bash also supports associative arrays (key-value pairs), although these require bash version 4 or higher. Here is how they work:

```bash
declare -A my_assoc_array
my_assoc_array[element1]="Hydrogen"
my_assoc_array[element3]="Lithium"
echo "${my_assoc_array[Helium]}"     #outputs element3
```

### sysadmin example

we work with a list of servers by hostname and want to check their connectivity quickly:

```bash
servers=("server1" "server2" "server3")

for server in "${servers[@]}"; do
  echo "Pinging $server..."
  ping -c 1 "$server" && echo "$server is up" || echo "$server is down"
done
```

#### Some Key Takeaways:

* Arrays are indexed collections that help organize and handle multiple values under one variable.
* Bash arrays support numerical indices by default, and with associative arrays, you can use string keys.
* Looping and indexing make arrays powerful for batch processing tasks in bash.

Bash arrays make handling lists much more efficient and are essential for more complex scripting tasks.

<details>

<summary>Rough Giraffe</summary>

An array can hold several values under one name.&#x20;

Array naming is the same as variables naming.

An Array is initialized by assignment of space-delimited values enclosed in ()

```bash
my_array=(apple banana "Fruit Basket" orange
new_array[2]=apricot
```

Array members need not be consecutive or contiguous. Some members of the array can be left uninitialized.

The total number of elements in the array is referenced by $(array\_name\[@]

```bash
my_array=(apple banana "Fruit Basket" orange)
echo ${#my_array[@]}                # 4
```

The array elements can be accessed by their numeric index. The index of the first element is 0.\


```bash
my_array=(apple banana "Fruit Basket" orange)
echo ${my_array[3]}                     # orange - note that curly brackets are needed
# adding another array element
my_array[4]="carrot"                    # value assignment without a $ and curly brackets
echo ${#my_array[@]}                    # 5
echo ${my_array[${#my_array[@]}-1]}     # carrot
```

</details>


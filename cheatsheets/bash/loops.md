---
description: 'bash has two(ish) kinds of loops: for and while loops ..'
---

# Loops

## for loop

```
# basic construct
for arg in [list]
do
    command(s)...
done
```

For each pass through the loop, `arg` takes on the value of each successive value in the list. Then the command(s) are executed.

```
# loop on array member
NAMES=(john jane kohn kane)
for N in ${NAMES[@]} ; do
    echo "name is $N"
done

# loop on command output results
for f in $( ls prog.sh /etc/localtime ) ; do
    echo "file is: $f"
done
```

## while loop

```
# basic construct
while [ condition ]
do
    command(s)...
done
```

The while construct test for a condition, and if true, executes commands. Keeps looping as long as the condition is true

```
COUNT=4
while [ $COUNT -gt 0]; do
    echo "value of count is $COUNT"
    COUNT=$(($COUNT -1))
done
```

## until loop

```
# basic construct
until [ condition ]
do 
    command(s)...
done
```

This `until` construct tests for a condition, if false, executes the command(s). It keeps looping as long as the condition is false (opposite of a `while` construct)

```
# prints out 0,1,2,3,4

COUNT=0
while [ $COUNT -ge 0 ]; do
    echo "value of COUNT is $COUNT"
    COUNT=$((COUNT+1))
    if [ $COUNT -ge 5 ] ; then
        break
    fi
done

# prints only odd numbers
COUNT=0
while [ $COUNT -lt 10 ]; do
    COUNT=$((COUNT+1))
    # check if COUNT is even number
    if [ $((COUNT % 2)) = 0 ] ; then
        continue
    fi
    echo $COUNT
done
```

---
description: >-
  Shell functions are reusable blocks of code that perform specific tasks,
  making our scripts more organized and efficient. They can be viewed as
  mini-scripts within a larger script, allowing y
---

# Functions

## creating a shell function

We will create a new file within our project directory called `functions.sh` to hold our functions

```bash
#!/bin/bash

# This is a simple function
greet() {
  echo "Hello, World!"
}

# This line calls (runs) the function
greet
```

Let's break this down:

* The first line `#!/bin/bash` is called a shebang. It tells the system to use bash to interpret this script.
* We define our function with `greet() { }`. Everything between the curly braces is part of the function.
* A simple `echo` command inside the function prints "Hello, World!".
* The last line `greet` calls (runs) our function.

#### Executing the script  holding our functions


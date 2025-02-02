---
description: and their fickle behavior ...
icon: square-root-variable
cover: ../../../.gitbook/assets/idk.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Special Variables

## Examples of Special Variable Behavior in the Shell

An ordinary variable can be expanded with `$VARNAME`. Commonly used variables like `PATH` and special variables like `PWD` and `RANDOM` have been covered - Further special expansions are documented in the following section, quoted verbatim from the bash man page

### Special Positional Variables

The shell treats several parameters specially. These parameters may only be referenced; assignment to them is not allowed.

|      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `$*` | Expands to the positional parameters (i.e., the command-line arguments passed to the shell script, with $1 being the first argument, $2 the second, etc.), starting from one. When the expansion occurs within double quotes, it expands to a single word with the value of each parameter separated by the first character of the IFS special variable. That is, "$\*" is equivalent to "$1c$2c...", where c is the first character of the value of the IFS variable. If IFS is unset, the parameters are separated by spaces. If IFS is null, the parameters are joined without intervening separators. |
| `$@` | Expands to the positional parameters, starting from one. Each parameter expands to a separate word when the expansion occurs within double quotes. That is, "$@" is equivalent to "$1" "$2" ... When there are no positional parameters, "$@" and $@ expand to nothing (i.e., they are removed). \[Hint: this is very useful for writing wrapper shell scripts that add one argument.]                                                                                                                                                                                                                    |
| `$#` | Expands to the number of positional parameters in decimal (i.e. the number of command-line arguments).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `$?` | Expands to the status of the most recently executed foreground pipeline. \[i.e., the exit code of the last command]                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `$-` | Expands to the current option flags as specified upon invocation, by the set builtin command, or those set by the shell itself (such as the -i option).                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `$$` | Expands to the process ID of the shell. In a () subshell, it expands to the process ID of the current shell, not the subshell.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `$!` | Expands to the process ID of the most recently executed background (asynchronous) command. \[i.e., after executing a background command with command &, the variable $! will give its process ID.]                                                                                                                                                                                                                                                                                                                                                                                                        |
| `$0` | Expands to the name of the shell or shell script. This is set at shell initialization. If bash is invoked with a file of commands, $0 is set to the name of that file. If bash is started with the -c option, then $0 is set to the first argument after the string to be executed if one is present. Otherwise, it is set to the file name used to invoke bash, as given by argument zero. \[Note that basename $0 is a useful way to get the name of the current command without the leading path.]                                                                                                     |
| `$-` | At shell startup, set to the absolute file name of the shell or shell script being executed as passed in the argument list. Subsequently, it expands to the last argument to the previous command after expansion. Also, it is set to the full file name of each command executed and placed in the environment exported to that command. When checking mail, this parameter holds the name of the mail file currently being checked.                                                                                                                                                                     |



Positional Variables are used to reference arguments passed to a shell script or a function.&#x20;

* The basic syntax is <mark style="color:green;">`${N}`</mark>, where <mark style="color:green;">`N`</mark> is a digit
  * The curly brackets can be omitted if <mark style="color:green;">`N`</mark> is a single digit. However, for consistency and best practice, use the curly brackets anyway.

#### Example - creating a function called `positional_variables` :&#x20;

```bash
function positional_variables() {
    echo "Positional variable 1: ${1}"
    echo "Positional variable 2: ${2}"
}
```

Next, invoke `postional_variables` by passing it two parameters:

```bash
$ positional_variables "one" "two"
Positional variable 1: one
Positional variable 2: two
```

So far, there is nothing special about this handling. Let's try to reference an argument that was not passed.

```bash
function positional_variables() {
    echo "Positional variable 1: ${1}"
    echo "Positional variable 2: ${2}"
    echo "Positional variable 3: ${3}"    
}
```

We see bash considers it unassigned:

```bash
$ positional_variables "one" "two"
Positional variable 1: one
Positional variable 2: two
Positional variable 3:
```

### All Arguments

Let's see what happens when we change the digit with the special character <mark style="color:green;">`@`</mark>&#x20;

{% code lineNumbers="true" %}
```bash
function positional_variables(){
    echo "Positional variables with @: ${@}"
}
```
{% endcode %}

The <mark style="color:green;">`${@}`</mark> construct expands to all the parameters passed to our function:

```bash
$ positional_variables "one" "two"
Positional variables with @: one two
```

Additionally, we can iterate over them, similar to an array:

{% code lineNumbers="true" %}
```bash
function positional_variables() {
    echo "Positional variables with @: ${@}"
    for element in ${@}
    do
        echo ${element}
    done
}
```
{% endcode %}

Running it and seeing what we get:

<pre class="language-bash"><code class="lang-bash"><strong>$ positional_variables "one" "two"
</strong>Positional variables with @: one two
one
two
</code></pre>

We can also use the <mark style="color:green;">`${∗}`</mark> construct to achieve the same result:

{% code lineNumbers="true" %}
```bash
function positional_variables(){
    echo "Positional variables with @: ${@}"
    echo "Positional variables with *: ${*}"
}
```
{% endcode %}

Take a look at the output:

```bash
$ positional_variables "one" "two"
Positional variables with @: one two
Positional variables with *: one two
```

At first look, the output is the same, right? Not exactly.

Let's change the <mark style="color:green;">`IFS`</mark> variable and rerun it:

{% code lineNumbers="true" %}
```bash
function positional_variables(){
    IFS=";"
    echo "Positional variables with @: ${@}"
    echo "Positional variables with *: ${*}"
}
```
{% endcode %}

Now, the output is:

```bash
$ positional_variables "one" "two"
Positional variables with @: one two
Positional variables with *: one;two
```

The difference here is the join between the input arguments.

<mark style="color:blue;">**When using**</mark>**&#x20;**<mark style="color:green;">**`${∗}`**</mark><mark style="color:blue;">**, the parameters are expanded to**</mark>**&#x20;**<mark style="color:green;">**`${1}c${2`**</mark><mark style="color:green;">**}**</mark>**&#x20;**<mark style="color:blue;">**and so on, where**</mark>**&#x20;**<mark style="color:green;">**`c`**</mark>**&#x20;**<mark style="color:blue;">**is the first character set in**</mark>**&#x20;**<mark style="color:green;">**`IFS`**</mark><mark style="color:blue;">.</mark>

Getting all inputs at once is very useful for input validation.

Let’s consider a simple utility function that checks if specific options are passed:

{% code lineNumbers="true" %}
```bash
function login(){
    if [[ ${@} =~ "-user" && ${@} =~ "-pass" ]]; then
        echo "Yehaww"
    else
        echo "Bad Syntax. Usage: -user [username] -pass [password] required"
    fi
}
```
{% endcode %}

In this snippet, we check all our function arguments for two specific flags.

Let’s focus on what happens differently with each call:

```bash
$ login -user "one" -pass "two" 
Yehaww
$ login -pass "two" -user "one" 
Yehaww
$ login "one" "two"
Bad Syntax. Usage: -user [username] -pass [password] required
```

Notice that the first two calls succeed regardless of the order of our inputs.

This allows the user to pass the arguments in any order but is a very rudimentary check:

```bash
$ login -user -pass
Yehaww
```

Notice that our condition has passed, although we only sent the two strings. We’ll see how to handle this next.

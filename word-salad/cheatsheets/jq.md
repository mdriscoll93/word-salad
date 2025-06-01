---
description: this stuff is black magic
icon: brackets-curly
cover: >-
  https://zwischenzugs.com/wp-content/uploads/2023/06/object_railroad_diagram.png
coverY: 0
layout:
  cover:
    visible: true
    size: full
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

# jq

{% hint style="info" %}
There is much more about this peculiar programming language that I cannot seem to wrap my head around. Writing out the parts I don't initially get and diagramming excessively seem to aid
{% endhint %}

## Conditionals and Comparisons

### The `if-then-else-end` conditional expression&#x20;

> to start, think of it like a basic coniditional in any other programming language ...

```jq
if (CONDITION) then (WHAT_TO_DO_IF_TRUE) else (WHAT_TO_DO_IF_FALSE_OR_NULL) end
```

#### The Rules of `jq`'s `if` :

* `CONDITION` is `truthy`⇒ run the `then` branch.
* `CONDITION` is `false` or `null` ⇒ run the `else` branch.
* No `else` is provided ⇒ defaults to outputting the current value `.`&#x20;

#### What is considered `truthy`  / `falsy` in `jq`?

* `truthy` : any value that is not `false` or `null`
* `falsy` : exactly `false` or `null` .

{% code title="# example #" lineNumbers="true" fullWidth="false" %}
```jq
echo '""' | jq 'if . then "true" else "false" end'              # Outputs: "true"
echo '""' | jq 'if. == "" then "empty" else "not empty" end'    # Outputs: "empty"
```
{% endcode %}

### More examples until it sticks:

{% code title="# simple number check #" lineNumbers="true" %}
```jq
echo '2' | jq 'if . == 0 then "zero" elif . == 1 then "one" else "many" end'
```
{% endcode %}

* input: `2`
* output: `"many"`&#x20;

***

{% code title="# missing .name field #" lineNumbers="true" %}
```jq
echo '{}' | jq 'if .name then "has name" else "no name" end'
```
{% endcode %}

* output: `"no name"`
  * because `.name` doesn't exist ⇒ `null` ⇒ `falsy`&#x20;

***

{% code title="# explicitly checking for an empty string #" lineNumbers="true" %}
```jq
echo '{"name":""}' | jq 'if .name == "" then "empty" else "not empty" end'
```
{% endcode %}

* output: `"empty"`

If you omit `== ""` thecheck would pass, because `""` is truthy in `jq`&#x20;

{% code title="# like so #" lineNumbers="true" %}
```jq
echo '{"name":""}' | jq 'if .name then "truthy" else "falsey" end'
```
{% endcode %}

* output: `"truthy"`&#x20;

***

### why was Mark confused?

* he has a smooth brain
* `jq`'s truthiness is simpler than Python or Javascript: only `false` and `null` are falsy. Everything else is truthy:
  * numbers
  * empty strings
  * empty arrays
  * empty objects

{% code lineNumbers="true" %}
```jq
echo '0' | jq 'if . then "truthy" else "falsey" end'   # truthy
echo '[]' | jq 'if . then "truthy" else "falsey" end'  # truthy
echo '{}' | jq 'if . then "truthy" else "falsey" end'  # truthy
```
{% endcode %}

***

| condition       | jq truthyness | notes                                  |
| --------------- | ------------- | -------------------------------------- |
| `null`          | falsy         | runs `else`                            |
| `false`         | falsy         | runs `else`                            |
| `0`             | truthy        | unlike python                          |
| `""`            | truthy        | unlike python                          |
| `[]`            | truthy        | unlike python                          |
| `{}`            | truthy        | unlike python                          |
| non-empty value | truthy        | always truthy unless `false` or `null` |

***

### how bout more examples

{% code title="# check a fields existence" lineNumbers="true" %}
```jq
echo '{"name": "Mark"}' | jq 'if .name then "yes" else "no" end'
# Output: "yes"

echo '{}' | jq 'if .name then "yes" else "no" end'
# Output: "no"
```
{% endcode %}

{% code title="# check exact string match # " lineNumbers="true" %}
```jq
echo '{"name": ""}' | jq 'if .name == "" then "empty" else "not empty" end'
# Output: "empty"
```
{% endcode %}

{% code title="# check zero value #" lineNumbers="true" %}
```jq
echo '0' | jq 'if . == 0 then "zero" else "not zero" end'
# Output: "zero"

echo '0' | jq 'if . then "truthy" else "falsey" end'
# Output: "truthy"
```
{% endcode %}

{% code title="# empty array or object #" lineNumbers="true" %}
```jq
echo '[]' | jq 'if . then "truthy" else "falsey" end'
# Output: "truthy"

echo '{}' | jq 'if . then "truthy" else "falsey" end'
# Output: "truthy"
```
{% endcode %}

#### Multi-branch with `elif`

{% code lineNumbers="true" %}
```jq
echo '2' | jq 'if . == 0 then "zero" elif . == 1 then "one" else "many" end'
# Output: "many"
```
{% endcode %}

```mermaid
flowchart TD
    classDef startStop fill:#f9f,stroke:#333,stroke-width:2px;
    classDef process fill:#bbf,stroke:#333,stroke-width:2px;
    classDef decision fill:#ffd,stroke:#333,stroke-width:2px;

    A["Start"]:::startStop
    B["Evaluate A (Condition)"]:::process
    C{"A is truthy?"}:::decision
    D["Run B (then branch)"]:::process
    E["Run C (else branch)"]:::process
    F["Stop"]:::startStop

    A --> B
    B --> C
    C -->|yes| D
    C -->|no| E
    D --> F
    E --> F


```

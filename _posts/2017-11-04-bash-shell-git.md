---
layout: post
title: Bash tips
category: tools
tags:
    - bash
---

## Bash tips
### script exits on error (when command has a non-zero exit status)
```
#!/bin/bash -e
set -e
```
### use *let* instead of *expr* to do arithmetic operation

```
i=1

j=$(expr $i - 1) 
echo "exit status is 1"

let "j = $i - 1" 
echo "exit status is 1"

let  "j = $i - 1" 1 
echo "exit status is 0"
```

- string substitution!! Never use the string substitution in bash **because it behaves differently in different versions**

```
str="a.b.c.d"

str1=${str/./'-'} 
echo str1 equals to 'a_b.c.d'

str1=$(echo $str | sed -r 's/\./-/')

str2=${str//./'-'}
echo str1 equals to 'a-b-c-d'

str="hello"
strLen=${#str}
echo "Length of string $str is $strlen"
```

- [$var, "$var" and ${var}. $array, $array[@], ${array[@]} and "${array[@]}"](https://stackoverflow.com/questions/18135451/what-is-the-difference-between-var-var-and-var-in-the-bash-shell)

- *jq* command

```
json='{"name":"list", "values":[{"type":"string", "value":"ok"},{"type":"string", "value":"not found"}]}'

echo $json | jq -r .name
echo  '*list*'

echo $json | jq -r .values | jq '. | length'
echo values length 2

echo $json | jq -r .values | jq -r .[0]
echo 'get the first value: {"type": "string", "value": "ok"}'
```

for keys containing '-', we have to use the following syntax to abstract the value:

```
echo '{"test-key": "value"}' | jq -r '.["test-key"]'
echo '{"test-key": "value"}' | jq -r '.["test-key"] = "new_value"'

```

- *sed*
```
sed '/^export/d' ~/.bash_history

sed 's/^export.*//' ~/.bash_history

```
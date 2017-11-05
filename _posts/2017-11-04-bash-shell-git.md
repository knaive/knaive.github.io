---
layout: post
title: Bash shell and git
category: tools
tags:
    - bash
    - git
---

## Bash shell
- script exits on error (when command has a non-zero exit status)
```
set -e
```
- use *let* instead of *expr* to do arithmetic operation

```
i=1

j=$(expr $i - 1) 
echo "exit status is 1"

let "j = $i - 1" 
echo "exit status is 1"

let  "j = $i - 1" 1 
echo "exit status is 0"
```

- string substitution

```
str="a.b.c.d"

str1=${str/./'-'} 
echo str1 equals to 'a_b.c.d'

str2=${str//./'-'}
echo str1 equals to 'a-b-c-d'

ls='l.s.'

${ls//./''}
echo execute *ls* command to list current directory
```

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

## Git
- unstage the last commit
```
git reset HEAD^
```
- [ undo the command above: git reset HEAD^ ](https://stackoverflow.com/questions/2510276/undoing-git-reset )
```
git reset 'HEAD@{1}'
```

- replace contents in the working dir with those in HEAD
```
git checkout -- file
```

- diff staged files
```
git diff --staged
```

- create a new branch
```
git branch NewBranchName
```

- switch to another branch
```
git checkout BranchName
```

- delete a branch
```
git branch -d BranchName
```

- add alias 'origin' for branch on remote server
```
git remote add origin <server>
```
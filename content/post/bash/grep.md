---
title: "Grep"
date: 2023-07-14T18:04:13+08:00
tags: [bash]
---

grep [options] PATTERN [FILE...]

grep [options] [-e PATTERN | -f FILE] [FILE...]

```
In basic regular expressions the metacharacters
  ? , + , { , | , ( , and  )
lose their special meaning; instead use the backslashed versions:
 \? ,\+ ,\{ ,\| ,\( , and \)
```

- -v (invert-match)
- -c (count)
- -m (max-count)
- -n (line-number)
- -L (files-without-match)
- -l (files-with-matches)


- --include=PATTERN
- --exclude=PATTERN


- -q(quiet)
- -s(no-messages)
- \> /dev/null


- -A(after)
- -B(before)
- -C(context)


- --binary-files=TYPE
> TYPE:
> - binary(default)
> - without-match(-I)
> - text(-a)

- --color
> GREP_COLOR
> - never
> - auto
> - always

- -G(basic-regexp, default)
- -E(extended-regexp)
- -P(perl-regexp)
- -F(fixed-strings)
```
grep -F -e "one" -e "two" file

grep -F 'one
two' file
```
- -f(file)


- -b(byte-offset)
- -u(unix-byte-offsets)

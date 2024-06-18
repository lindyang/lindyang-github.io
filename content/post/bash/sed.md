---
title: "Sed"
date: 2024-06-04T14:13:37+08:00
tags: [bash]
---

```
    SED_EXPR+='''
h;
s/^\( *\).*/\1/;
x;
:loop;
  n;
  G;  # 添加缩进
  s/^\( *\)\([^ ].*\)\n\1$/\1\2/;  # 同级判断
  t read;  # 同级则跳出 loop 循环
  s/\n *$//;  # 恢复
/^''';
  SED_EXPR+="$YAML_INNER_INDENT";
  SED_EXPR+='''addresses:/!b loop;
:hypen;
  $b eof;
  N;
  /\n *$\|\n *\(\#\|- \)[^\n]*$/b hypen;  # 空行|注释行|数组行
:eof;
  /^ *addresses:[^\n]*\] *$\|\n *$\|\n *\(\#\|- \)[^\n]*$/d;  # 以"单行[]|空行|注释行|数组行"抵达文件末尾
  s/.*\n\([^\n]*\)$/\1/;  # 包含其它行
:read; n; b read;
}''';
```
[link](https://unix.stackexchange.com/questions/648684/how-do-i-remove-all-specific-sub-sections-of-a-specific-header-in-a-yaml-file/765234?noredirect=1#comment1459228_765234)

```
sed '/^ *\/image-content:/{
:sub;
  $b eof;  # end of file
  N;
/^\( *\)[^ ].*\n\1[^ ][^\n]*$/!b sub;  # leading-spaces==ending-spaces(\1). loop if not same level
:eof;
  # if join with the first-line of next block, only leave the joint-line.
  s/^\( *\)[^ ].*\n\(\1[^ ][^\n]*\)$/\2/;
  t loop;  # jump if s/././ is done
  d;  # no more lines after target block
:loop; n; b loop;  # b loop is to speed the process
}' file;
```

```
re_m = re.compile(r"""
            ^Device\ management\ user\ (\S+):$
            (?:\n^\ .+)+
            \n^\ +Service\ type:\ +\S*Terminal\S*$
            (?:\n^\ .+)+
            \n^\ +User\ role\ list:\ +network-admin$
        """, re.X | re.M)
users = re.findall(re_m, ret)
```

---
title: "Wd"
date: 2023-07-28T15:58:20+08:00
tags: [misc]
---


```bash
#!/bin/bash

# usage: ./dict_youdao.sh word

tmpf=/tmp/dict_youdao.txt

# get web page sourcecode
curl -s "http://dict.youdao.com/search?q=$1" > $tmpf

if [ $(ls -l $tmpf | awk '{printf $5}') = "0" ];then
	echo "error: curl failed"
	exit
fi

# set start/end line number of translation area in sourcecode
div_start_line=$(grep -n '<div id="phrsListTab" class="trans-wrapper clearfix">' $tmpf | cut -d ':' -f1)
div_end_line=$(grep -n '<div id="webTrans" class="trans-wrapper trans-tab">' $tmpf | cut -d ':' -f1)

# cutoff useless infomation in sourcecode
sed -e "1,${div_start_line:=1}d" -e "${div_end_line:=1},\$d" -i $tmpf	# strange here, need to add a '\' before the $d

# show the result
grep -o ">\[.*\]<" $tmpf | sed -r 's/>|<//g' | xargs -0 echo "br/us: "
grep -o "<li>.*</li>" $tmpf | sed 's/<.\{,1\}li>//g'
```



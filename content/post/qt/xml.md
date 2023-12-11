---
title: "Xml"
date: 2023-09-22T13:40:54+08:00
tags: [qt]
---

# 示例

```
<?xml version="1.0" encoding="utf-8"?>
<!-- some comment -->
<!DOCTYPE a [
<!ENTITY reg "&#174;">
<!ENTITY diams "&#9830;">
]>
<a>
        <name>abc</name>
        <age>&reg;</age>
        <grade>&diams;</grade>
		<script>
<![CDATA[
function matchwo(a,b)
{
if (a < b && a < 0) then
  {
  return 1;
  }
else
  {
  return 0;
  }
}
]]>
		</script>

</a>
```


# 解析 XML 的三种方式
- QXmlStreamReader
- DOM
- SAX

#### token 类型
tokenString:
    - StartDocument
    - EndElement
    - Characters
    - DTD
    - comment
    - Invalid

### 函数辨析
readNext:
    Reads the next token and returns its type.
    - StartDocument:
    - StartElement: <school>
    - Characters: "\n    ", 或者元素中的内容

readNextStartElement:


readElementText:
    Convenience function to be called in case a StartElement was read.
    Reads until the corresponding EndElement and returns all text in-between.
    读取 value


- name 和 text 基本上互斥

name:
    Returns the local name of a StartElement, EndElement, or an EntityReference.
text:
    Returns the text of Characters, Comment, DTD, or EntityReference.
    - "\n    "
    - "some comment" for <!-- some comment-->
    - "\n<!DocTYPE school[]>"


### 两个特殊的类型

EntityReference:
    &lt; <
    &gt: >
    &amp; &
    &apos; '
    &quot; "

DTD:
    <!DOCTYPE persons[]>


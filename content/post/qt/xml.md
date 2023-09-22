---
title: "Xml"
date: 2023-09-22T13:40:54+08:00
tags: [qt]
---

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


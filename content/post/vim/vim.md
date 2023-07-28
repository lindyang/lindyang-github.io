---
title: "Vim"
date: 2023-07-28T14:32:49+08:00
tags: [vim]
---


# Chapter 1: Basic Editing
### alias
- h: <Left>, <BS>, CTRL-H and CTRL-K
- j: <Down>, <NL>, CTRL-J, and CTRL-N
- k: <Up> and CTRL-P
- l: <Right> and <Space>
### undo
- If undo too many times, press CTRL-R (redo) to reverse the preceding command.
- U(undo line)
> Second U undoes the preceding U

### edit
- a: append after the cursor
- 3a!<Esc>

### help
-  :help (:h, <F1>, <Help>)
- To get out of the help system, use `ZZ`
-  |:help| (颜色是 cyan)
- CTRL-] (jump to tag)
- CTRL-T (pop tag)
- `*help.txt*`
> - help deleting
> - help CTRL-A  "default normal mode
> - help i_CTRL-H  "mode
> - help -t  "option
> - help 'number'  "set nu
> - help <Up>  "Special keys
![image.png](https://upload-images.jianshu.io/upload_images/14001410-0521dfe61d407325.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*Vim是Ex行编辑器的可视模式。或者说，Ex是Vim的底层行编辑器*
- set helplang=de,it  "Vim always searches English as a last resort.

### tutor
- vimtutor
- help tutor

# Chapter 2: Editing a Little Faster
### word movement
- $, <End>, and <kEnd>. (The <kEnd> key is Vim's name for the keypad End key.)
- 2$
- ^
- <Home> or <kHome> `0` much faster
- 5f<Space>
- t: one character before
- 3G
- 25%
- %

### Scrolling Up and Down
- CTRL-U
- CTRL-D

### Delete
- d3w or 3dw  "3d2w
- D=d$

### Change
- cc
- c$=C

### Join lines
- J

### Replace
-  r{char}
- `5ra` to replace the first five characters with a
-  5r<Enter> replaces five characters with `one` <Enter>
### Case
- ~
### Keyboard Macros
- q{character}...q
- @<reg>
### Digraphs
- dig
- CTRL-K{c}{c}

# Chapter 3: Searching
-  .*[]^%/\?~$
- q/  "History Window, q?
- :nohlsearch  "To clear the current highlighting

# Chapter 4: Text Blocks and Multiple Files
-  p command placed the text on the line `after` the cursor
- V: line visual mode
- v: simple visulal mode
- Marks
```
'{mark}moves to the beginning .
`{mark} moves to the marked line and column.
```
- d'a
- :marks
![image.png](https://upload-images.jianshu.io/upload_images/14001410-9928b32538b6325c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1204)
- Y = yy, 不同于 D 或 C
### Filtering
- !sort<Enter>
- !!ls
- !!date
### edit another file
- :e! file  "close current
- :enew  "new unnamed buffer
- :view file  "readonly, use `:write!` force save
- :wnext
- :set autowrite
- :set 'autowriteall
- :args  "which File Am I On
- :Next 和 next 不同
- :rewind  "first
-  register (#) contains the name of last file.
- CTRL-^ to alternative
-  [count]CTRL-^ goes to the count file on the command line.
- :match `Error` /TOOD/  ":highlight show highlight names
- :match none
```
There are can be three matches active at one time.
These are set by the :match, :2match, and :3match commands.
If some text is matched by more than one command, the lowest one wins.
```

# Chapter 5: Windows and Tabs
- CTRL-Wc (CTRL-W CTRL-C)
- :split +/printf three.c
- CTRL-Wn (CTRL-W CTRL-N, :new)
- :new, :vnew
- :vertical :sview
- CTRL-W=
- count CTRL-W_
- :resize
### buffers
```
If it has a window on the screen, it is a buffer;
if it is not on the screen, it is not a buffer.
```
- :hiden
```
Active Appears onscreen.
Hidden A file is being edited, but does not appear onscreen.
Inactive The file is not being edited, but keep the information about it anyway.
```
- :buffers can also be written as :ls, :files
```
- Inactive buffer.
h Buffer is hidden.
% Current buffer.
# Alternate buffer.
+ File has been modified.
```
- :sbuffer number
- :vertical sbuffer number
- :sbmodified count
- :set hidden
### Tabs
- :tabnew, tabedit
- :tabclose 2
- :tabonly
- :tabnext 3
- gt (<C-PageDown>)
- gT (<C-PageUp>)
- :tabfirst
- :tablast
```
The CTRL-Wgf command opens a new tab and starts to edit the file who's
name is under the cursor. Vim will search for the file using the same algorithm
as it uses for the :find command. The CTRL-WgF command does the same thing,
only it goes a step farther. It not only starts editing the file in a new tab, but
jumps to the line number following the file name.
```
- :tabfind

Chapter 6: Basic Visual Mode

---
title: "Lua"
date: 2023-07-28T14:34:20+08:00
tags: [lua]
---


5.3 加入 64-bit integer， 以前只有 double-precision floating;
small lua: 32-bit integers and single-precision floats;
Both integer and float values have type "number".

- math.type

floating-point hexadecimal constants
> 0xa.bp2  -- (10 + 11/16) * 4， Lua 5.2 引入

string.format("%a", 0.1)

division of two integers does not need to be an integer

5.3 引入 //

modulo always having the same sign as the second argument.

math.pi - math.pi % 0.01

~=

math.min(1, 2)
math.huge -- inf
math.randomseed(os.time())
math.random()  -- [0, 1), die [1, n], [l, u]
math.rad
math.deg
floor, ceil, and modf  -- modf rounds towards zero
math.modf(-3.3)  -- -3, -0.3

> -math.mininteger == math.mininteger
> math.maxinteger + 2.0 == math.maxinteger + 1.0  -- true

lose precision

> 2^53 | 0  -- integer
-- number has an exact representation as an integer

math.tointeger(5.01)  -- nil, not an integral value
math.tointeger(2^64)  -- nil, out of range

^
unary operators  (-  #  ~  not)
*  /  //  %
+  -
..  (concatentation)
<<  >>  (bitwise shifts)
&
(bitwise AND)
~  (bitwise exclusive OR)
|  (bitwise OR)
<  >  <=  >=  ~=  ==
and
or

All binary operators are left associative, 
except for exponentiation and concatenation, 
which are right associative.

Lua 5.2 can represent exact integers only up to 2^53 , 
while in Lua 5.3 the limit is 2^63
> (2^62|0) + (2^62|0) -1 == math.maxinteger

> math.maxinteger * math.maxinteger
> math.mininteger * math.mininteger

Strings in Lua are immutable values.

print(#"你h")  -- 4， in bytes

'ok' .. 2  -- If any operand is a number, Lua converts this number to a string:

\'
\"
\ddd  \xhh \u{hhh}  -- \ddd 是 10 进制

long string, ignore the first character
[[....]]
[===[.......]===]

data = "\x00\x01\z
        \x08"

automatic conversions between numbers and strings at run time
> "10" + 1  -- 11.0 不完美的 lua
强大的 tonumber 和 math.tointeger(" 10")
tonumber 可以使用进制
tonumber("-ZZ", 36)
tonumber("9", 8)  -- nil
tostring, 但是还是建议用 string.format

string.len  -- #s
string.rep
string.reverse
string.sub(s, i, j) -- [i, j]
string.char(...)
string.byte(''[, start[, end]])  -- limit stack size

s:sub(i, j)
string.find("hello world", "war")  -- nil

string.gsub 返回 2 个值

reverse,upper, lower, byte, and char 
do not work for UTF-8 strings, 
as all of them assume that one character
is equivalent to one byte.

utf8.len("ab\x93") -- nil 3
utf8.char(0x4f60)  -- 你
utf8.codepoint(s, utf8.offset(s, 5))
utf8.codes

nil 不能作为 table 的 key

like global variables, 
table fields evaluate to nil when not initialized.

tbl[2] == tbl[2.0]
2 compares equal to 2.0
tbl[1/3] 也是合法的
a = {x = 10, y = 20}
we cannot initialize fields with negative
indices, nor with string indices that are not proper identifiers.

tbl 可以用, 或 ; 分隔

without holes a sequence

The length operator is unreliable for lists with holes (nils)

a table with no numeric keys is a sequence with length zero.

If you really need to handle lists
with holes, you should store the length explicitly somewhere.

io.lines()
table.insert
table.remove

-- insert begin
table.move(a, 1, #a, 2)
a[1] = newElement

-- remove first
table.move(a, 2, #a, 1)  -- copy
a[#a] = nil

table.move 从一个到另外一个tbl

### function
if the function has
one single argument and that argument is either a literal string or a table constructor, then the parentheses
are optional:

function with a number of arguments different from its number of parameters. Lua adjusts
the number of arguments to the number of parameters by throwing away extra arguments and supplying
nils to extra parameters.

· When we use a call as an expression
(e.g., the operand of an addition), Lua keeps only the first result.
· We get all results only when the call is
the last (or the only) expression in a list of expressions.

print(foo2(), 1)  -- a 1
print(foo2() .. 'x')  -- ax


t = {foo0(), foo2(), 4} -- t[1] = nil, t[2] = "a", t[3] = 4

We can force a call to return exactly one result by enclosing it in an extra pair of parentheses.

ipairs{...}
local a, b = ...
local arg = table.pack(...)  -- arg.n
select(2, "a", "b", "c")  -- b c
select('#', a, b, c)  -- 3

table.unpack
table.unpack({"Sun", "Mon", "Tue", "Wed"}, 2, 3)  -- Mon Tue

io.input(filename)
io.output
避免 io.write(a..b..c)
print automatically applies tostring to its arguments

io.read
a reads the whole file
l drop newline read  -- default
L read a line
n read a number
[*]num reads num chars as a string

io.lines()
table.sort

io.read(0)  -- test for end of file
local n1, n2, n3 = io.read("n", "n", "n")

io.open -- nil 'error msg'

local f = assert(io.open(filename, "r"))
local t = f:read("a")
f:close()

io.stderr:write(message)
io.input()  -- 
io.input():close()

for block in io.input():lines(2^13) do
    io.write(block)
end

io.tmpfile  -- read/write
io.flush()

setvbuf(mode[, bufsize])
no
full
line

f:seek(whence, offset)
set, cur, end
return current position of the stream, measured in bytes
from the beginning of the file.

function fsize (file)
local current = file:seek()
local size = file:seek("end")
file:seek("set", current)
return size
end

os.rename
os.remove

os.exit(true, true)
os.getenv("HOME")
os.execute  -- true exit/signal return_status

f = io.popen(cmd, "w")
f = io.popen(cmd, "r)
f:close()
f:lines()

local to a control, function, chunk, then
do-end

strict.lua
raises an error if we try to assign to a non-existent global inside a function or to use a non-existent global.

local foo = foo

until terminates repeat structures

Lua has no switch statement

repeat–until statement repeats its body until its condition is true.

for var is local
do return end
::name::

table.sort(network, function (a,b) return (a.name > b.name) end)

Lib = {}
function Lib.foo (x, y) return x + y end

local fact
fact = function (n)
  if n == 0 then return 1
  else return n * fact(n-1)
  end
end

do
    Lua sandboxes
end

find, gsub, match, gmatch
string.find("a [word]", "[", 1, true)  --> 3  3
string.gsub("all lii", "l", "x", 1)
When in doubt, play safe and use an escape.

balanced strings, %b()

%f[char-set]
string.gsub ("THE (QUICK) brOWN FOx JUMPS", "%f[%a]%u+%f[%A]", print)
"%f[%w]the%f[%W]"  -- only as an entire word
d, m, y = string.match(date, "(%d+)/(%d+)/(%d+)")

_G

string.gsub(s, "\\(%a+){(.-)}", "<%1>%2</%1>")

function toxml (s)
  s = string.gsub(s, "\\(%a+)(%b{})", function (tag, body)
    body = string.sub(body, 2, -2) -- remove the brackets
    body = toxml(body)  -- handle nested commands
    return string.format("<%s>%s</%s>", tag, body, tag)
  end)
  return s
end

print(toxml("\\title{The \\bold{big} example}"))
--> <title>The <bold>big</bold> example</title>

table.concat

print(string.match("hello", "()ll()"))  --> 3  5

wday  -- one is sunday
yday  -- one is january 1st
isdst -- true is daylight saving is in effect

os.time({...})
os.date('*t', 0)
os.date("!%c", 0)  -- utc
os.difftime
os.clock

bitwise operators only work on integer values

~ (bitwise exclusive-OR)

Lua does not offeran arithmetic right shift

a >> n is the same as a << -n

If the displacement is equal to or larger than the number of bits in the integer representation (64 in Standard
Lua, 32 in Small Lua), the result is zero, as all bits are shifted out of the result

he standard way to print numbers interprets
them as signed integers. We can use the %u or %x options in string.format to see integers as unsigned

math.ult

flip the signal bit of both operands
(0x7fffffffffffffff ~ mask) < (0x8000000000000000 ~ mask)

f = (u + 0.0) % 2^64  -- do not need 0.0

u = math.tointeger(((f + 2^63) % 2^64) - 2^63)

string.unpack("z", s, i)  -- zero-terminated string

b (char),
h (short),
i (int), and l (long); 
the option j uses the size of a Lua integer.

i1 - i16

s = table.concat(t, "\n") .. "\n"

string.format('%a', 1/3)
string.format("%q", a)  -- works for nil and boolean

loadfile, dofile
f = load('i = i + 1')
-- load always compiles its chunks in the global environment.

A common mistake is to assume that loading a chunk defines functions
f = loadfile("foo.lua")
print(foo) --> nil
f() -- run the chunk
foo("ok") --> ok


luac -o prog.lc prog.lua

string.dump

pcall
xpcall  -- debug.debug, debug.traceback

local f = require "mod".foo  -- (require("mod")).foo


require

package.loaded
package.path

If require cannot find a Lua file with the module name, 
it searches for a C library with that name.

package.cpath

package.loadlib

luaopen_modname

package.loaded[@rep{modname}]

local mod = require "mod".init(0, 0)

LUA_PATH_5_3 -> LUA_PATH -> compiled-defined default path
LUA_PATH_5_3 to "mydir/?.lua;;  -- ;; 很重要
LUA_CPATH_5_3 or LUA_CPATH.
package.searchpath

lua -E

package.searchers
package.preload

package.loaded[...] = M

: if a module does not return a value, require will return the current value of
package.loaded[modname] (if it is not nil).

p -- p/init.lua
p.a  -- p/a.lua  luaopen_a_b

getmetatable
setmetatable

__metatable
__pairs
__index
mt.__index = prototype
rawget
__newindex
rawset(t, k, v)
local mt = {__index = function () return d end}
t[{}] = d

function Account:withdraw (v)
  self.balance = self.balance - v
end

getmetatable(a).__index.deposit(a, 100.00)

rawset, which bypasses the metamethod

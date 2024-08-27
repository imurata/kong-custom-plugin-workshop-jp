## コメント

```lua
-- コメント
--[[ 複数行の
     コメント ]]
```

## 関数呼び出し

```lua
print()
print("Hi")

-- 引数が文字列またはテーブル形式の場合は括弧を省略できます。
print "Hello World"     <-->     print("Hello World")
dofile 'a.lua'          <-->     dofile ('a.lua')
print [[a multi-line    <-->     print([[a multi-line
 message]]                        message]])
f{x=10, y=20}           <-->     f({x=10, y=20})
type{}                  <-->     type({})
```

## テーブル / 列

```lua
t = {}
t = { a = 1, b = 2 }
t.a = function() ... end

t = { ["hello"] = 200 }
t.hello

-- 列はテーブルでもある
array = { "a", "b", "c", "d" }
print(array[2])       -- "b" (one-indexed)
print(#array)         -- 4 (length)
```

## 繰り返し

```lua
while condition do
end

for i = 1,5 do
end

for i = start,finish,delta do
end

for k,v in pairs(tab) do
end

repeat
until condition

-- Break文
while x do
  if condition then break end
end
```

## 条件式

```lua
if condition then
  print("yes")
elseif condition then
  print("maybe")
else
  print("no")
end
Variables
local x = 2
two, four = 2, 4
Functions
function myFunction()
  return 1
end

function myFunctionWithArgs(a, b)
  -- ...
end

myFunction()

anonymousFunctions(function()
  -- ...
end)

-- モジュールにExportされない
local function myPrivateFunction()
end

-- Splats（可変引数）
function doAction(action, ...)
  print("Doing '"..action.."' to", ...)
  --> print("Doing 'write' to", "Shirley", "Abed")
end

doAction('write', "Shirley", "Abed")
Lookups
mytable = { x = 2, y = function() .. end }

-- 以下は同じ意味:
mytable.x
mytable['x']

-- 糖衣構文。以下は同じ意味:
mytable.y(mytable)
mytable:y()

mytable.y(mytable, a, b)
mytable:y(a, b)

function X:y(z) .. end
function X.y(self, z) .. end
Metatables
mt = {}

-- メタテーブルは、単に関数を含むテーブルです。
mt.__tostring = function() return "lol" end
mt.__add      = function(b) ... end       -- a + b
mt.__mul      = function(b) ... end       -- a * b
mt.__index    = function(k) ... end       -- Lookups (a[k] or a.k)
mt.__newindex = function(k, v) ... end    -- Setters (a[k] = v)

-- メタテーブルを使用すると、別のテーブルの動作をオーバーライドできます。
mytable = {}
setmetatable(mytable, mt)

print(myobject)
```

## クラス

```lua
Account = {}

function Account:new(balance)
  local t = setmetatable({}, { __index = Account })

  -- コンストラクタ
  t.balance = (balance or 0)
  return t
end

function Account:withdraw(amount)
  print("Withdrawing "..amount.."...")
  self.balance = self.balance - amount
  self:report()
end

function Account:report()
  print("Your current balance is: "..self.balance)
end

a = Account:new(9000)
a:withdraw(200)    -- メソッド呼び出し
```

## 定数

```lua
nil
false
true
```

## 演算子 (とメタテーブルの名前)

```lua
-- 関係演算子 (binary)
-- __eq  __lt  __gt  __le  __ge
   ==    <     >     <=    >=
~=   -- Not equal, != のようなもの

-- 算術演算子 (binary)
-- __add  __sub  __muv  __div  __mod  __pow
   +      -      *      /      %      ^

-- 算術演算子 (単項)
-- __unm (単項でのマイナス)
   -

-- 論理演算子 (and/or)
nil and false  --> nil
false and nil  --> false
0 and 20       --> 20
10 and 20      --> 20


-- 長さ
-- __len(array)
#array


-- インデックス
-- __index(table, key)
t[key]
t.key

-- __newindex(table, key, value)
t[key]=value

-- 文字列の連結
-- __concat(left, right)
"hello, "..name

-- 呼び出し
-- __call(func, ...)
```

## API: Global function (ref)

```lua
dofile("hello.lua")
loadfile("hello.lua")

assert(x)    -- x かどうか (errorをあげる)
assert(x, "failed")

type(var)   -- "nil" | "number" | "string" | "boolean" | "table" | "function" | "thread" | "userdata"

-- メタメソッドを呼び出さない (__index and __newindex)
rawset(t, index, value)    -- Like t[index] = value
rawget(t, index)           -- Like t[index]

_G  -- グローバルコンテキスト
setfenv(1, {})  -- 1: 現在の関数, 2: 呼び出し元など -- {}: the new _G

pairs(t)     -- {key, value}の反復可能リスト
ipairs(t)    -- {index, value}の反復可能リスト

tonumber("34")
tonumber("8f", 16)
API: Strings
'string'..'concatenation'

s = "Hello"
s:upper()
s:lower()
s:len()    -- #s のようなもの

s:find()
s:gfind()

s:match()
s:gmatch()

s:sub()
s:gsub()

s:rep()
s:char()
s:dump()
s:reverse()
s:byte()
s:format()
```

## API: Tables

```lua
table.foreach(t, function(row) ... end)
table.setn
table.insert(t, 21)          -- append (--> t[#t+1] = 21)
table.insert(t, 4, 99)
table.getn
table.concat
table.sort
table.remove(t, 4)
```

## API: Math (ref)

```lua
math.abs     math.acos    math.asin       math.atan    math.atan2
math.ceil    math.cos     math.cosh       math.deg     math.exp
math.floor   math.fmod    math.frexp      math.ldexp   math.log
math.log10   math.max     math.min        math.modf    math.pow
math.rad     math.random  math.randomseed math.sin     math.sinh
math.sqrt    math.tan     math.tanh

math.sqrt(144)
math
```

## API: その他

```lua
io.output(io.open("file.txt", "w"))
io.write(x)
io.close()

for line in io.lines("file.txt")

file = assert(io.open("file.txt", "r"))
file:read()
file:lines()
file:close()
```

## Reference

http://www.lua.org/pil/13.html
http://lua-users.org/wiki/ObjectOrientedProgramming

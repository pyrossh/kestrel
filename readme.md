module lambda

Sample syntax
```
fun sum(a: int, b: int): int = a + b

fn sum_list(series: list[int]): int =
  series.reduce(0, |v| v + 1)

fn fib(n: int): int =
  match n
    0 | 1 -> n
    else -> fib(n - 1) + fib(n - 2)

fn fib(n: int): int =
  if n == 0 || n == 1 then
    n
  else
    fib(n - 1) + fib(n - 2)
  end

fn factorial(n: int): int =
  match n
    1 -> 1
    _ -> n * factorial(n - 1)

fn to-celsius(f: float) =
  (f - 32) * (5 / 9)

record Cat(
  name: str
  age:  int
)

fn Cat.WithName(name: str) =
  Cat(name: name, age: 0)

fn (c: Cat) fullname(): str =
  c.name + c.age.to_str()

fn (c: Cat) to_str(): str =
  "Cat<{c.fullname()}>"

test("Cat", |v|
  c := Cat(name = "123", age = 1)
  c.talk()
  Cat("rabby", 21).fullname() == "rabby21"
  c2 := Cat(...c, age: c.age + 1)

  [Cat("Molly", 9), Cat("Fenton", 6)]
    .retain(|p| p.name.size > 5)
    .map(|p| describe(p))
    .each(|d| println(d))
  // → cat Fenton is 6 years of age
)

type MapCallback = fn(v: a): v

trait Comparable[A](
  fn compare(left: A, right: A): bool
)

trait ToStr(
  fn to_str(): str
)

struct Cat(name: str, age: int) =
  fn fullname() =
    name + age.str()

  fn talk() =
    println("cat ${name} says meow")

  #[ToStr]
  fn to_str(self): str =
    "Cat<{self.fullname()}>"


enum Temperature =
  | celsius(float)
  | fahrenheit(float)

fn (s Temperature) to_str() =
  match s
    celsius(t) && t > 30 -> "${t}C is above 30 celsius"
    celsius(t) -> "${t}C is below 30 celsius"
    fahrenheit(t) && t > 86 -> "${t}F is above 86 fahrenheit"
    fahrenheit(t) -> "${t}F is below 86 fahrenheit"

test "enum ordinal value" {
  expect(Value.zero).to_equal(0);
  expect(Value.one).to_equal(1);
  expect(Value.two).to_equal(2);
}

test("enum ordinal value") |t| {
  expect(Value.zero).to_equal(0);
  expect(Value.one).to_equal(1);
  expect(Value.two).to_equal(2);
}

group("Group 1") |g| {
}

bench("1231") |t, n| {
}
```

## Language Reference

**Keywords**
unreachable

**Types**
bool, byte, int, float, dec, str, time, duration
[] for lists      list[int], list[list[int]]
[] for maps       map[int], map[map[int]]
? for optional    int? str?
! for return error types int!, str!
nil for optional assignment and pointers

## Types

**nil**
A nil type is used to represent types that are nilable.

**bool**
A bool can be either `true` or `false`. It is used in logical operations and conditional statements.

```
assert true != false

if true || false:
  print("works")
```

**byte**
A byte represents an unsigned 8 bit number. It is mainly used to represent strings and binary data.
```
let data: []byte?
data = [104, 101, 197, 130, 197, 130, 111, 0]
```

**int**
An int is a signed 64 bit number. It can be represented in various ways,
0b - Binary (Base 2)
0x - Hexadecimal (Base 16)
13 - Standard (Base 10)

```
0b00101010
0b1_1111_1
0xff00ff
0xFF00FF
0xFF80_0000_0000_0000
98762
1_000_000
```

**float**
A float represents a 64-bit floating point (52-bit mantissa) IEEE-754-2008 binary64

**str**
A str represents a slice of runes or unicode code points. It is encoded to UTF-8 by default.
It supports interpolation of variables/values that implement the ToStr interface.

```
"Hello World"
name := "Pacos"
age := 1
println("Name ${name} age ${age}")
```
"\u0061" == "a"


**list**

```
a := [1, 2, 3]
```

**map**

```
tree = [
  value: "Fred",
  left: [
    value: "Jim",
  ],
  right: [
    value: "Shiela",
    left: [
      value: "Alice",
    },
    right: [
      value: "Bob"
    ],
  ],
]
```


**Assignment operator**
low, mid, high := 0, 0, n.numItems
x := 10
y := 20
xy_list := [x, y]
xy_map := [x: x, y: y]
assoc_list = [a: 1, b: 2]
assoc_list[:a]
assoc_list["b"]

**While statement**
while low < high
  mid = (low + high) / 2
  low = cmp > 0 > mid + 1 : low
  high = cmp < 0 ? mid : high
  if cmp == 0
    return mid, true

while (i < 10) : (i += 1) {}

while (eventuallyErrorSequence()) |value| {
  sum1 += value;
else |err|
  try expect(err == error.ReachedZero);

**For statement**
for players_list |value|
  if value == 0
    continue
  sum += value

for items[0..1] |v|
  sum += v

for 0..5 |v|
  sum3 += v

let list = for filter(players_list) |v|

items
  .map(|v| v + 1)
  .each(|v| println("v", v))

items
  .each(v =>
    if v == 2
      break
    if v == 0
      continue
    println(v)
  )
  .map {v => v * 2}
  .reduce(0, |v| => v + 1)

fn range(start: int, end: int, cb: (v: T) -> IterateResult) =

range(0, 5, |v| =>
  sum3 += v
)

// Iterate over multiple objects.
// All lengths must be equal at the start of the loop, otherwise detectable
// illegal behavior occurs.
for items, items2 |i, j|
  count += i + j

**When expression/statement**
when
  cmp > 0 -> low = mid + 1
  cmp < 0 -> high = mid
  cmp == 0 -> return mid, true

Arithmetic (+, -, /, *, @divFloor, @sqrt, @ceil, @log, etc.)
Bitwise operators (>>, <<, &, |, ~, etc.)
Comparison operators (<, >, ==, etc.)

**if expression/statement**
if (a) |value| {
 try expect(value == 0);
} else |err| {
    _ = err;
    unreachable;
}

### 5. Conditional operators

**not operator**
!a
!true

**ternary operator**
x ? x : y

**safe navigation operator**
a?.b?.c?.d

**elvis operator**
x ?: y

**elvis assignment operator**
atomicNumber ?= 2

**Range operator**
for 5..10 |i|:

**Safe index operator**
array?[1]
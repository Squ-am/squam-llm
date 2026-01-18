# Squam Language Reference

Squam is a statically-typed scripting language with Rust-like syntax, type inference, generics, traits, and async/await.

## Basics

```squam
fn main() {
    let x = 42;              // Type inferred as i64
    let y: f64 = 3.14;       // Explicit type
    let mut counter = 0;     // Mutable variable
    counter = counter + 1;
    println(f"x = {x}");     // String interpolation with f"..."
}
```

## Types

| Type | Description | Literal |
|------|-------------|---------|
| `bool` | Boolean | `true`, `false` |
| `i64` | Default integer | `42`, `0xFF`, `0b1010` |
| `f64` | Default float | `3.14`, `2e10` |
| `String` | Text | `"hello"` |
| `char` | Unicode char | `'a'`, `'\n'` |
| `()` | Unit | `()` |
| `[T; N]` | Array | `[1, 2, 3]` |
| `(T, U)` | Tuple | `(1, "hi")` |
| `Option<T>` | Nullable | `Some(x)`, `None` |
| `Result<T,E>` | Fallible | `Ok(x)`, `Err(e)` |

**Ranges:** `0..10` (exclusive), `0..=10` (inclusive)

## Functions

```squam
fn add(a: i64, b: i64) -> i64 {
    a + b  // Implicit return (no semicolon)
}

fn greet(name: String) {
    println(f"Hello, {name}!");  // No return = ()
}

// Generics
fn identity<T>(x: T) -> T { x }

// With trait bounds
fn print_all<T: Display>(items: [T]) {
    for item in items { println(item.to_string()); }
}
```

## Control Flow

```squam
// Conditionals (are expressions)
let status = if x > 0 { "positive" } else { "non-positive" };

// Loops
while condition { ... }
loop { if done { break; } }
for item in iterable { ... }
for i in 0..10 { ... }

// Pattern matching
match value {
    0 => "zero",
    1 | 2 => "small",
    3..=9 => "medium",
    n if n < 0 => "negative",
    _ => "large",
}
```

## Structs

```squam
struct Point { x: f64, y: f64 }
struct Config { timeout: i64 = 30 }  // Default value

impl Point {
    fn new(x: f64, y: f64) -> Point { Point { x, y } }
    fn distance(&self) -> f64 { sqrt(self.x * self.x + self.y * self.y) }
    fn translate(&mut self, dx: f64, dy: f64) { self.x = self.x + dx; self.y = self.y + dy; }
}

let p = Point::new(3.0, 4.0);  // Associated function
let d = p.distance();          // Method call
```

## Enums

```squam
enum Status { Pending, Active, Done }
enum Message {
    Quit,
    Move { x: i64, y: i64 },
    Text(String),
}

// Generic enums (Option/Result are built-in)
enum Either<L, R> { Left(L), Right(R) }

match msg {
    Message::Quit => quit(),
    Message::Move { x, y } => move_to(x, y),
    Message::Text(s) => println(s),
}
```

## Traits

```squam
trait Display {
    fn to_string(&self) -> String;
}

impl Display for Point {
    fn to_string(&self) -> String { f"({self.x}, {self.y})" }
}
```

## Closures

```squam
let add = |a, b| a + b;
let double = |x| x * 2;
let complex = |x| { let y = x * 2; y + 1 };

// Higher-order functions
let doubled = arr_map([1,2,3], |x| x * 2);  // [2, 4, 6]
let evens = arr_filter([1,2,3,4], |x| x % 2 == 0);  // [2, 4]
```

## Error Handling

```squam
fn parse_num(s: String) -> Result<i64, String> {
    parse_int(s)  // Returns Result
}

fn process(s: String) -> Result<i64, String> {
    let n = parse_num(s)?;  // ? propagates Err early
    Ok(n * 2)
}

// Option handling
match opt {
    Some(v) => use(v),
    None => default(),
}
let val = unwrap_or(opt, default);
```

## Async/Await

```squam
async fn fetch() -> String {
    async_sleep_secs(1.0);
    "data"
}

fn main() {
    // Spawn concurrent tasks
    let h1 = spawn(fetch());
    let h2 = spawn(fetch());

    // Wait for results
    let r1 = block_on(h1);
    let r2 = block_on(h2);
}
```

## Modules

```squam
mod math {
    pub fn add(a: i64, b: i64) -> i64 { a + b }
    fn private_helper() { }
}

use math::add;
let sum = add(1, 2);
// or: math::add(1, 2)
```

## Destructuring

```squam
let (a, b) = (1, 2);
let Point { x, y } = point;
let [first, second, ..rest] = array;

if let Some(value) = optional { use(value); }
```

## Standard Library Quick Reference

**Strings:**
`str_len`, `str_concat`, `str_substring(s, start, end)`, `str_contains`, `str_split(s, delim)`, `str_join(arr, sep)`, `str_trim`, `str_to_upper`, `str_to_lower`, `str_replace(s, from, to)`, `parse_int`, `parse_float`

**Arrays:**
`arr_len`, `arr_push`, `arr_pop`, `arr_get`, `arr_set`, `arr_map(arr, fn)`, `arr_filter(arr, fn)`, `arr_reduce(arr, fn, init)`, `arr_sort`, `arr_reverse`, `arr_find(arr, fn)`, `arr_any`, `arr_all`

**Math:**
`abs`, `min`, `max`, `clamp`, `floor`, `ceil`, `round`, `sqrt`, `pow`, `sin`, `cos`, `tan`, `pi()`, `e()`

**I/O:**
`print`, `println`, `read_line`, `input(prompt)`, `read_file`, `write_file`, `file_exists`

**Collections:**
`HashMap::new()`, `map_insert`, `map_get`, `map_remove`, `map_keys`, `map_values`
`HashSet::new()`, `set_insert`, `set_contains`, `set_remove`

**Time:**
`time_now()`, `sleep(ms)`, `async_sleep(ms)`, `async_sleep_secs(s)`

**Random:**
`random()`, `random_int_range(min, max)`, `random_choice(arr)`, `shuffle(arr)`

**JSON:**
`json_parse(s)`, `json_stringify(value)`

**Network:**
`tcp_connect(host, port)`, `tcp_listen(host, port)`, `tcp_accept`, `tcp_read`, `tcp_read_line`, `tcp_write`, `tcp_close`

**Type checks:**
`typeof(v)`, `is_int`, `is_float`, `is_string`, `is_bool`, `is_array`

**Debug:**
`dbg(value)`, `assert(cond)`, `assert_eq(a, b)`, `panic(msg)`

## Operators

**Arithmetic:** `+`, `-`, `*`, `/`, `%`, `**` (power)
**Comparison:** `==`, `!=`, `<`, `<=`, `>`, `>=`
**Logical:** `&&`, `||`, `!` (short-circuit)
**Bitwise:** `&`, `|`, `^`, `~`, `<<`, `>>`
**Assignment:** `=`, `+=`, `-=`, `*=`, `/=`, etc.
**Other:** `..` (range), `?` (try), `as` (cast)

## Complete Example

```squam
struct Task {
    id: i64,
    title: String,
    done: bool = false,
}

impl Task {
    fn new(id: i64, title: String) -> Task {
        Task { id, title }
    }

    fn complete(&mut self) {
        self.done = true;
    }
}

fn main() {
    let mut tasks = [
        Task::new(1, "Learn Squam"),
        Task::new(2, "Build something"),
    ];

    tasks[0].complete();

    let pending = arr_filter(tasks, |t| !t.done);
    println(f"Pending: {arr_len(pending)}");

    for task in tasks {
        let status = if task.done { "done" } else { "pending" };
        println(f"[{status}] {task.title}");
    }
}
```

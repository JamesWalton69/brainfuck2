# brainfuck — a Brainfuck project written in Brainfuck

A self-hosting Brainfuck project: an interpreter for Brainfuck whose source
is written entirely in Brainfuck.

## Files

| File | What it is |
|---|---|
| `interpreter.bf` | A full Brainfuck interpreter written in Brainfuck (596 bytes). Original by **Daniel B. Cristofani** (`dbfi.b`, a clone of Frans Faase's design — see `http://www.hevanet.com/cristofd/brainfuck/dbfi.b`). |
| `hello_world.bf` | Example program: prints `Hello World!`. |
| `cat.bf` | Example program: echoes its input back. |
| `food_order.bf` | An online food ordering system (interactive: menu → item → quantity → receipt), written 100% in Brainfuck. |

## Running it

You need any normal Brainfuck runtime (tape >= 30000 cells, 8-bit wrapping
cells). Feed the interpreter the *program* and its *input* on stdin,
separated by an exclamation point `!`:

```
bf-run interpreter.bf < hello_world.bf!
bf-run interpreter.bf < cat.bf!hello
```

The `!` separates the program to be interpreted from the input that the
interpreted program reads. End-of-input (EOF) is delivered as `0`.

Run `cat.bf`'s input through the interpreter with:

```
bf-run interpreter.bf < cat.bf!hello
# prints: hello
```

## Food ordering system

`food_order.bf` is an interactive terminal order flow (Brainfuck has no
networking, so "online" = a live interactive session). Opening the file shows
a comment header explaining what it is; running it first prints an "About"
banner, then the menu. It loops on pairs of digits typed on stdin: first
digit = item (1–5), second digit = quantity. Sending EOF (`0` byte / end of
input) as the next item starts checkout and prints a receipt with line items
and total.

```
1 Burger $5    4 Pizza $8
2 Fries  $3    5 Salad  $6
3 Soda   $2
```

Example session (input `12` → two burgers, `0` ends):

```
Item? Qty? Item? Burger x2
Total: $10
```

Verified flow (in a reference simulator, 8-bit wrapping cells):

| Input | Receipt | Total |
|---|---|---|
| `12` | Burger x2 | $10 |
| `12130` | Burger x5 | $25 |
| `520` | Salad x2 | $12 |
| `31` | Soda x1 | $2 |
| `111` | Burger x1 | $5 |
| `44` | Pizza x4 | $32 |
| `51` | Salad x1 | $6 |
| `11510` | Burger x1, Salad x1 | $11 |
| `0` | (nothing) | $0 |
| `9` | Bad (ignored) | $0 |

A non-menu digit prints `Bad` and is ignored; EOF as quantity cancels that
line.

## Semantics supported

- All 8 commands, nested loops, the full 256-value byte range.
- EOF translates to `0` (`LB` behavior in Cristofani's test suite).
- Non-command characters in the input are ignored.

## Verification

`interpreter.bf` was executed inside a reference simulator against:

| Test | Input | Expected | Result |
|---|---|---|---|
| `hello_world.bf` | — | `Hello World!` | ✅ |
| nested loops (`++++[>++++[>+++<-]<-]>>.`) | — | `0` | ✅ |
| Cristofani's `tests.b` I/O probe | `\n` | `LB\nLB` (EOF=0) | ✅ |
| Cristofani's `rot13.b` (deep nesting) | `~mlk zyx` | `~zyx mlk` | ✅ |

## Credits

Interpreter: Daniel B. Cristofani — `http://www.hevanet.com/cristofd/brainfuck/dbfi.b`

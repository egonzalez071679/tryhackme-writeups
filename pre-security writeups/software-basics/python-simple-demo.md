# Python: Simple Demo — TryHackMe

> **Path:** Software Basics module  
> **Difficulty:** Easy  
> **Category:** Programming Fundamentals  
> **Completed:** 2026-05-05  
> **Room Link:** https://tryhackme.com/room/pythonsimpledemo

---

## Summary

Introductory Python room building a "Guess the Number" game. Walks through Python's core building blocks: variables, library imports, user input, type conversion, conditional statements (`if`/`elif`/`else`), comparison operators, counter patterns, and loops. The game itself is a vehicle for teaching the fundamentals.

---

## Key Concepts Learned

- **Variables** — named storage; Python is dynamically typed and requires no declaration
- **Library imports** — `import random` brings in random number generation; Python's standard library is rich
- **`input()` and type conversion** — `input()` always returns a string; `int()` converts to integer (raises `ValueError` on bad input)
- **Conditional statements** — `if` / `elif` / `else` with indentation-based blocks
- **Comparison and logical operators** — `==` `!=` `<` `>` `<=` `>=` `and` `or` `not`
- **Counter pattern** — `tries = tries + 1` (or Pythonic `tries += 1`)
- **Iteration via `while` loops** — runs until condition becomes false

---

## The exercise

Build a Guess the Number game with three rules:
1. The computer secretly picks a number between 1 and 20
2. The user keeps guessing until they get it right
3. The computer tells the user whether their guess is too low or too high

Expected session:

```
ubuntu@tryhackme:~$ python guess_the_number.py
I'm thinking of a number between 1 and 20
Take a guess: 10
Too high, try again.
Take a guess: 5
Too low, try again.
Take a guess: 7
Too low, try again.
Take a guess: 8
You got it in 4 tries!
```

---

## Walkthrough

### Step 1 — Setup (variables and the secret number)

```python
import random          # gives us tools for picking random numbers

secret = random.randint(1, 20)   # a <= secret <= b (inclusive both ends)
tries = 0
guess = 0                # start with a value that cannot be the secret

print("I'm thinking of a number between 1 and 20")
```

Notes:
- `random.randint(1, 20)` is **inclusive on both ends** — both 1 and 20 are possible outcomes. This differs from most other languages where the upper bound is exclusive.
- `guess = 0` is set to a value that can't be the secret, so the loop runs at least once.

### Step 2 — Reading user input

```python
text = input("Take a guess: ")   # input() returns text (a string)
guess = int(text)                # convert the text to a number

tries = tries + 1                # add 1 try (written long-form for clarity)
```

Critical points:
- `input()` **always** returns a string, even if the user types a number.
- `int(text)` converts the string. If the user types `"hello"` instead of a number, this raises `ValueError`.
- Forgetting the type conversion is the #1 beginner Python bug — comparing a string to an integer (`"7" < 8`) raises `TypeError` in Python 3.

### Step 3 — The conditional logic

```python
# Give a hint using if / elif / else.
if guess < 1 or guess > 20:
    print("That number is out of range. Try again.")
elif guess < secret:
    print("Too low, try again.")
elif guess > secret:
    print("Too high, try again.")
else:
    print("You got it in", tries, "tries!")
```

Order matters:
1. **Out-of-range check first** — catches invalid input before treating it as a guess
2. **`elif guess < secret`** — guess is valid but too low
3. **`elif guess > secret`** — guess is valid but too high
4. **`else`** — guess matches secret (only remaining possibility)

Python uses **indentation** (typically 4 spaces) to define code blocks. There are no curly braces. Misaligned indentation is a syntax error, not just bad style.

### Step 4 — The loop (making it playable)

The initial draft of the program runs through ONCE and stops. To make it a real game, wrap the input + conditional in a `while` loop that runs until the guess matches:

```python
while guess != secret:
    text = input("Take a guess: ")
    guess = int(text)
    tries = tries + 1

    if guess < 1 or guess > 20:
        print("That number is out of range. Try again.")
    elif guess < secret:
        print("Too low, try again.")
    elif guess > secret:
        print("Too high, try again.")
    else:
        print("You got it in", tries, "tries!")
```

`while <condition>:` runs as long as the condition is true. The body must do something that eventually makes the condition false — otherwise the loop runs forever (infinite loop).

In this game: each iteration reads a guess. Eventually `guess == secret`, the condition `guess != secret` becomes false, and the loop exits.

---

## Complete working program

```python
import random

secret = random.randint(1, 20)
tries = 0
guess = 0

print("I'm thinking of a number between 1 and 20")

while guess != secret:
    text = input("Take a guess: ")
    guess = int(text)
    tries = tries + 1

    if guess < 1 or guess > 20:
        print("That number is out of range. Try again.")
    elif guess < secret:
        print("Too low, try again.")
    elif guess > secret:
        print("Too high, try again.")
    else:
        print("You got it in", tries, "tries!")
```

---

## Python concept reference

### Operators

| Operator | Meaning |
|----------|---------|
| `==` | Equal to |
| `!=` | Not equal to |
| `<` `>` | Less than / greater than |
| `<=` `>=` | Less or equal / greater or equal |
| `and` | Both must be true |
| `or` | At least one must be true |
| `not` | Negation |

### Common gotchas

- **Indentation is syntax.** 4 spaces (or tabs) define a code block. Inconsistent indentation = error.
- **`input()` returns strings, always.** Convert with `int()`, `float()`, or string methods as needed.
- **`int()` raises ValueError** on bad input. Wrap in `try / except` for resilience.
- **`==` is comparison, `=` is assignment.** Mixing them is the classic C-family bug.
- **`random.randint(a, b)` is inclusive both ends.** Different from most languages.

### Pythonic shortcuts

- `tries += 1` instead of `tries = tries + 1`
- `f"You got it in {tries} tries!"` (f-string) instead of `print("...", tries, "...")`
- Comments use `#`; docstrings use `"""triple quotes"""`

---

## Takeaways

- **Variables, conditionals, and loops are the foundation of every triage script.** Even simple analyst tools — log parsers, IOC enrichers, alert formatters — are built from these three primitives. Mastering them on a small game is a low-stakes way to internalize the syntax.
- **`input()` and type conversion generalize to log parsing.** Reading CSV columns, JSON fields, or user-supplied IOCs all involve the same string-to-typed-value pattern as `int(input())`. Forgetting the conversion step is a beginner bug; remembering it is the foundation of safer parsing.
- **`int()` raising `ValueError` previews the need for `try/except`.** Production analyst scripts wrap conversions in error handling to deal with malformed log entries gracefully. This room introduces the bug; the next step is learning the defense.
- **Indentation discipline matters.** Python's whitespace-as-syntax forces visual clarity. Scripts that handle real data benefit from this — it prevents the bracket-nesting mistakes common in C-family languages.
- **Short-circuit evaluation in `and`/`or`** is genuinely useful as a safe guard: `if data and data.startswith('xyz'):` won't crash if `data` is None. Worth internalizing early.
- **`random.randint()` appears in security contexts beyond toy programs** — honeypot design, password/token generation, synthetic traffic generation, fuzz testing. Knowing the inclusive-both-ends behavior is a small but real Python-specific detail.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/pythonsimpledemo)
- [Python 3 Documentation — Built-in Functions](https://docs.python.org/3/library/functions.html)
- [Python 3 Documentation — random module](https://docs.python.org/3/library/random.html)
- [Python 3 Documentation — Control Flow](https://docs.python.org/3/tutorial/controlflow.html)

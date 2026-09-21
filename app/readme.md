Yep — this line is confusing because it combines **three different Kotlin/Compose concepts**:

```kotlin
var result by remember { mutableStateOf(1) }
```

You can understand it as:

> **"Create a variable called `result` whose value is remembered by Compose, and make that value observable state starting at 1."**

Let's break it apart.

### 1. `mutableStateOf(1)`

You already understand this part:

```kotlin
mutableStateOf(1)
```

It creates a piece of Compose state whose initial value is `1`.

Conceptually:

```text
state
 └── value = 1
```

The important thing is that the state object isn't actually the number `1`. It is an object that **contains** the number `1`.

---

### 2. `remember { ... }`

`remember` tells Compose:

> **"Keep this thing around when you recompose this function."**

For example:

```kotlin
val number = remember { mutableStateOf(1) }
```

Without `remember`, every time Compose recomposes your UI, it could create a brand-new state object:

```text
Recomposition #1 → state = 1
Recomposition #2 → state = 1
Recomposition #3 → state = 1
```

With `remember`:

```text
First composition → create state = 1
                       ↓
Recomposition → use SAME state
                       ↓
Recomposition → use SAME state
```

So `remember` is essentially saying:

> "Compose, remember the object I create here."

---

### 3. So why `by`?

This is the **Kotlin part**, not really a Compose part.

Without `by`, you'd write:

```kotlin
val result = remember { mutableStateOf(1) }
```

Now `result` is the **state object**.

To get the actual number, you'd have to write:

```kotlin
result.value
```

And to change it:

```kotlin
result.value = 6
```

So:

```kotlin
val result = remember { mutableStateOf(1) }

println(result.value)

result.value = 6
```

---

With Kotlin's `by` syntax:

```kotlin
var result by remember { mutableStateOf(1) }
```

Kotlin essentially lets you treat the state object's `.value` as if it were the variable itself.

So instead of:

```kotlin
result.value
```

you can write:

```kotlin
result
```

And instead of:

```kotlin
result.value = 6
```

you can write:

```kotlin
result = 6
```

### The comparison

These two accomplish essentially the same thing:

```kotlin
// Without `by`
var result = remember { mutableStateOf(1) }

result.value = 6
```

versus:

```kotlin
// With `by`
var result by remember { mutableStateOf(1) }

result = 6
```

The `by` is using Kotlin's **property delegation** feature. You don't need to teach students the full mechanics of property delegation to understand Compose initially.

For a beginner-friendly explanation, I'd phrase it as:

> **`remember` tells Compose to keep the state between recompositions. `by` lets us work with the state's value directly instead of writing `.value` every time.**

So for your dice example:

```kotlin
var result by remember { mutableStateOf(1) }
```

means:

> "Make a piece of state called `result`, start it at 1, and remember it when the UI redraws."

Then:

```kotlin
result = Random.nextInt(1, 7)
```

changes the state, which causes Compose to redraw the parts of the UI that depend on `result`.

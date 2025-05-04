
Here's a concise summary of key points about `BigInt` in JavaScript:

### ✅ What is `BigInt`?

`BigInt` is a built-in primitive type used to represent **arbitrary large integers** beyond the `Number.MAX_SAFE_INTEGER`.

### ✅ How to create a `BigInt`

```js
const a = 123n; // Using 'n' suffix
const b = BigInt("123"); // Using constructor
```

### ✅ Characteristics

* `typeof 123n === 'bigint'`
* Cannot mix `BigInt` with `Number` in arithmetic operations.
* Use relational comparisons with care: `1n < 2` is valid.
* `BigInt` operations truncate division: `5n / 2n === 2n`

### ✅ Unsupported

* `Math` functions (`Math.pow`, `Math.sqrt`, etc.)
* `>>>` (unsigned right shift)
* Unary `+`
* JSON.stringify directly throws

### ✅ Workarounds for JSON

```js
const replacer = (k, v) => typeof v === 'bigint' ? v.toString() + 'n' : v;
const reviver = (k, v) => typeof v === 'string' && v.endsWith('n') ? BigInt(v.slice(0, -1)) : v;
```

### ⚠️ Warnings

* Not safe for cryptography (due to timing attacks)
* Avoid converting between `BigInt` and `Number` to prevent precision loss

Would you like a cheat sheet image for this?


The `BigInt()` constructor is used to create **BigInt values**, which represent arbitrarily large integers.

### 🔹 Syntax

```js
BigInt(value)
```

> ⚠️ Do **not** use `new BigInt()` — it will throw a `TypeError`.

---

### 🔹 Parameters

* `value`: Can be a string, number (safe integer only), boolean, or object convertible to BigInt.

---

### 🔹 Examples

```js
// From a number
BigInt(123);           // 123n

// From a string
BigInt("123");         // 123n

// From a hex string
BigInt("0x1fffff");    // 2097151n

// From a boolean
BigInt(true);          // 1n
BigInt(false);         // 0n

// Invalid: fractional number
BigInt(1.5);           // ❌ RangeError

// Invalid: unsafe number
BigInt(1e100);         // ❌ TypeError (loss of precision)
```

---

### 🔹 Notes

* Safer to pass **strings** than large numbers to avoid precision issues.
* `typeof BigInt("1") === "bigint"`.

Would you like a comparison of `BigInt` vs `Number` usage in practice?


Here’s a quick overview of the static methods `BigInt.asIntN()` and `BigInt.asUintN()` in JavaScript:

---

### 🔹 `BigInt.asIntN(bits, bigint)`

Converts a BigInt to a **signed integer** in 2’s complement, of length `bits`.

```js
BigInt.asIntN(8, 257n);    // => 1n (because 257 mod 256 = 1)
BigInt.asIntN(8, -129n);   // => 127n (wraps to fit in signed 8-bit)
```

* `8` means: treat as a signed 8-bit integer (`-128` to `127` range).
* Wraps around on overflow.

---

### 🔹 `BigInt.asUintN(bits, bigint)`

Converts a BigInt to an **unsigned integer** of `bits` length.

```js
BigInt.asUintN(8, 257n);   // => 1n (257 mod 256 = 1)
BigInt.asUintN(8, -1n);    // => 255n (wraps as unsigned)
```

* `8` means: treat as an unsigned 8-bit integer (`0` to `255` range).

---

### 🔹 When to Use

* For **bit-level operations**, e.g., simulating hardware or low-level APIs.
* To emulate fixed-width integer arithmetic (like C-style `int8`, `uint32`, etc.).

Need a quick visual chart of common bit sizes and their ranges?


Here’s a concise summary of the **instance methods** of `BigInt`:

---

### 🔹 `toString([radix])`

Returns the string representation of the BigInt in the given base.

```js
123456789n.toString();      // "123456789"
255n.toString(16);          // "ff"
```

---

### 🔹 `toLocaleString([locales], [options])`

Formats the BigInt using locale-specific conventions.

```js
123456789n.toLocaleString();                  // "123,456,789" (in en-US)
123456789n.toLocaleString('de-DE');           // "123.456.789"
```

---

### 🔹 `valueOf()`

Returns the primitive BigInt value (rarely used directly).

```js
const x = 42n;
x.valueOf();               // 42n
```

---

These methods mirror the behavior of `Number` but are for BigInts. Want a cheat sheet comparing these to `Number` methods?


In JavaScript, `BigInt` instances **inherit** from `BigInt.prototype`, similar to how numbers inherit from `Number.prototype`.

### 🔹 Inheritance chain:

```
BigInt instance
   ↓
BigInt.prototype
   ↓
Object.prototype
```

So, any `BigInt` object has access to:

* Its own methods like `.toString()`, `.toLocaleString()`, `.valueOf()`
* Generic `Object` methods like `.hasOwnProperty()`, `.toString()` (overridden), etc.

### Example:

```js
const big = 100n;

console.log(big instanceof BigInt);           // false (primitive)
console.log(Object(big) instanceof BigInt);   // true (object wrapper)

console.log(big.toString());                  // "100"
```

Although `big` is a **primitive**, it's auto-wrapped when calling methods, just like with numbers or strings.



Here’s the **browser compatibility** for `BigInt`:

### ✅ Supported in:

* **Chrome**: 67+
* **Edge**: 79+
* **Firefox**: 68+
* **Safari**: 14+
* **Opera**: 54+
* **Node.js**: 10.4.0+ (with `--harmony-bigint`), fully in 12+

### ❌ Not supported in:

* **Internet Explorer**: ❌ Not supported at all

> ⚠️ If targeting older browsers like IE11, avoid using `BigInt` or use polyfills with limited support.

Need a fallback or polyfill approach for BigInt?


`BigInt` can’t be fully polyfilled since it introduces a new primitive type. However, you can **simulate** some functionality using libraries or workarounds:

### ✅ Use a library:

* **[JSBI](https://github.com/GoogleChromeLabs/jsbi)** by Google — a pure-JS implementation that mimics `BigInt` behavior using standard numbers/objects.

```bash
npm install jsbi
```

**Example:**

```js
import JSBI from 'jsbi';

const a = JSBI.BigInt('9007199254740993');
const b = JSBI.BigInt('10');
const sum = JSBI.add(a, b);

console.log(JSBI.toNumber(sum)); // 9007199254741003
```

### ⚠️ Limitations:

* No support for `typeof x === 'bigint'`.
* Cannot override native `BigInt`, so code changes are needed.

Would you like a helper wrapper to use JSBI with fallback detection?

### ✅ `bigint-wrapper.js`

```js
// bigint-wrapper.js
import JSBI from 'jsbi';

const isNativeBigInt = typeof BigInt !== 'undefined';

const BigIntWrapper = {
  from(value) {
    return isNativeBigInt ? BigInt(value) : JSBI.BigInt(value);
  },
  add(a, b) {
    return isNativeBigInt ? a + b : JSBI.add(a, b);
  },
  subtract(a, b) {
    return isNativeBigInt ? a - b : JSBI.subtract(a, b);
  },
  multiply(a, b) {
    return isNativeBigInt ? a * b : JSBI.multiply(a, b);
  },
  divide(a, b) {
    return isNativeBigInt ? a / b : JSBI.divide(a, b);
  },
  toNumber(value) {
    return isNativeBigInt ? Number(value) : JSBI.toNumber(value);
  }
};

export default BigIntWrapper;
```

### ✅ Usage

```js
import BigIntWrapper from './bigint-wrapper.js';

const a = BigIntWrapper.from('9007199254740993');
const b = BigIntWrapper.from('7');
const sum = BigIntWrapper.add(a, b);

console.log(BigIntWrapper.toNumber(sum)); // 9007199254741000
```



Here’s the updated `BigIntWrapper` with comparison and bitwise operations added:

### ✅ `bigint-wrapper.js` (Extended)

```js
import JSBI from 'jsbi';

const isNativeBigInt = typeof BigInt !== 'undefined';

const BigIntWrapper = {
  from(value) {
    return isNativeBigInt ? BigInt(value) : JSBI.BigInt(value);
  },
  add(a, b) {
    return isNativeBigInt ? a + b : JSBI.add(a, b);
  },
  subtract(a, b) {
    return isNativeBigInt ? a - b : JSBI.subtract(a, b);
  },
  multiply(a, b) {
    return isNativeBigInt ? a * b : JSBI.multiply(a, b);
  },
  divide(a, b) {
    return isNativeBigInt ? a / b : JSBI.divide(a, b);
  },
  remainder(a, b) {
    return isNativeBigInt ? a % b : JSBI.remainder(a, b);
  },
  toNumber(value) {
    return isNativeBigInt ? Number(value) : JSBI.toNumber(value);
  },
  toString(value) {
    return isNativeBigInt ? value.toString() : JSBI.toString(value);
  },

  // Comparison
  equal(a, b) {
    return isNativeBigInt ? a === b : JSBI.equal(a, b);
  },
  lessThan(a, b) {
    return isNativeBigInt ? a < b : JSBI.lessThan(a, b);
  },
  greaterThan(a, b) {
    return isNativeBigInt ? a > b : JSBI.greaterThan(a, b);
  },

  // Bitwise
  bitwiseAnd(a, b) {
    return isNativeBigInt ? a & b : JSBI.bitwiseAnd(a, b);
  },
  bitwiseOr(a, b) {
    return isNativeBigInt ? a | b : JSBI.bitwiseOr(a, b);
  },
  bitwiseXor(a, b) {
    return isNativeBigInt ? a ^ b : JSBI.bitwiseXor(a, b);
  },
  leftShift(a, b) {
    return isNativeBigInt ? a << b : JSBI.leftShift(a, b);
  },
  signedRightShift(a, b) {
    return isNativeBigInt ? a >> b : JSBI.signedRightShift(a, b);
  },
  unsignedRightShift(a, b) {
    return isNativeBigInt ? a >>> b : JSBI.unsignedRightShift(a, b);
  },
};

export default BigIntWrapper;
```


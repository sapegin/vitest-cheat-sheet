<!--
To update TOC run:
  npx markdown-toc --maxdepth 3 -i Readme.md

To reformat run:
  npx prettier --print-width 100 --single-quote --no-semi --prose-wrap never --write Readme.md
-->

<div align="center" markdown="1">

<img src="https://vitest.dev/logo.svg" width="200">

<h1>Vitest cheat sheet</h1>

</div>

_This is a fork of the popular [Jest cheat sheet](https://github.com/sapegin/jest-cheat-sheet)._

_I’m writing [a book on clean code for frontend developers](https://sapegin.me/book/)._

## Table of contents

<!-- toc -->

- [Getting started](#getting-started)
- [Test structure](#test-structure)
  - [Basic test structure](#basic-test-structure)
  - [Test with grouping, setup and teardown code](#test-with-grouping-setup-and-teardown-code)
- [Matchers](#matchers)
  - [Basic matchers](#basic-matchers)
  - [Truthiness](#truthiness)
  - [Numbers](#numbers)
  - [Strings](#strings)
  - [Arrays](#arrays)
  - [Objects](#objects)
  - [Exceptions](#exceptions)
  - [Snapshots](#snapshots)
  - [Misc](#misc)
  - [Promise matchers](#promise-matchers)
- [Async tests](#async-tests)
  - [async/await](#asyncawait)
  - [Promises](#promises)
- [Mocks](#mocks)
  - [Mock functions](#mock-functions)
  - [Returning, resolving and rejecting values](#returning-resolving-and-rejecting-values)
  - [Mock modules using `vi.mock()` method](#mock-modules-using-vimock-method)
  - [Mock modules using a mock file](#mock-modules-using-a-mock-file)
  - [Mock object methods](#mock-object-methods)
  - [Mock getters and setters](#mock-getters-and-setters)
  - [Clearing and restoring mocks](#clearing-and-restoring-mocks)
  - [Accessing the original module when using mocks](#accessing-the-original-module-when-using-mocks)
  - [Timer mocks](#timer-mocks)
- [Data-driven tests](#data-driven-tests)
- [Skipping tests](#skipping-tests)
- [Testing modules with side effects](#testing-modules-with-side-effects)
- [Resources](#resources)
- [Contributing](#contributing)
- [Sponsoring](#sponsoring)
- [Author and license](#author-and-license)

<!-- tocstop -->

## Getting started

[See the official docs](https://vitest.dev/guide/)

## Test structure

### Basic test structure

> [!INFO]  
> In comparison to Jest, in Vitest you need to explicitly import all methods

```js
import { expect, test } from 'vitest'
import { makePoniesPink } from './makePoniesPink'

test('make each pony pink', () => {
  const actual = makePoniesPink(['Alice', 'Bob', 'Eve'])
  expect(actual).toEqual(['Pink Alice', 'Pink Bob', 'Pink Eve'])
})
```

### Test with grouping, setup and teardown code

```js
import { expect, test, beforeAll, afterAll , beforeEach,afterEach} from 'vitest'
import { makePoniesPink } from './makePoniesPink'

describe('makePoniesPink', () => {
beforeAll(() => {
  // Runs before all tests
})
afterAll(() => {
  // Runs after all tests
})
beforeEach(() => {
  // Runs before each test
})
afterEach(() => {
  // Runs after each test
})

test('make each pony pink', () => {
  const actual = makePoniesPink(['Alice', 'Bob', 'Eve'])
  expect(actual).toEqual(['Pink Alice', 'Pink Bob', 'Pink Eve'])
})
```

## Matchers

[Matchers docs](https://vitest.dev/api/expect.html)

### Basic matchers

```js
expect(42).toBe(42) // Strict equality (===)
expect(42).not.toBe(3) // Strict equality (!==)
expect([1, 2]).toEqual([1, 2]) // Deep equality
expect({ a: undefined, b: 2 }).toEqual({ b: 2 }) // Deep equality
expect({ a: undefined, b: 2 }).not.toStrictEqual({ b: 2 }) // Strict equality (Jest 23+)
```

### Truthiness

```js
// Matches anything that an if statement treats as true (true, 1, 'hello', {}, [], 5.3)
expect('foo').toBeTruthy()
// Matches anything that an if statement treats as false (false, 0, '', null, undefined, NaN)
expect('').toBeFalsy()
// Matches only null
expect(null).toBeNull()
// Matches only undefined
expect(undefined).toBeUndefined()
// The opposite of toBeUndefined
expect(7).toBeDefined()
// Matches true or false
expect(true).toEqual(expect.any(Boolean))
```

### Numbers

```js
expect(2).toBeGreaterThan(1)
expect(1).toBeGreaterThanOrEqual(1)
expect(1).toBeLessThan(2)
expect(1).toBeLessThanOrEqual(1)
expect(0.2 + 0.1).toBeCloseTo(0.3, 5)
expect(NaN).toEqual(expect.any(Number))
```

### Strings

```js
expect('long string').toMatch('str')
expect('string').toEqual(expect.any(String))
expect('coffee').toMatch(/ff/)
expect('pizza').not.toMatch('coffee')
expect(['pizza', 'coffee']).toEqual([expect.stringContaining('zz'), expect.stringMatching(/ff/)])
```

### Arrays

```js
expect([]).toEqual(expect.any(Array))
expect(['Alice', 'Bob', 'Eve']).toHaveLength(3)
expect(['Alice', 'Bob', 'Eve']).toContain('Alice')
expect([{ a: 1 }, { a: 2 }]).toContainEqual({ a: 1 })
expect(['Alice', 'Bob', 'Eve']).toEqual(expect.arrayContaining(['Alice', 'Bob']))
```

### Objects

```js
expect({ a: 1 }).toHaveProperty('a')
expect({ a: 1 }).toHaveProperty('a', 1)
expect({ a: { b: 1 } }).toHaveProperty('a.b')
expect({ a: 1, b: 2 }).toMatchObject({ a: 1 })
expect({ a: 1, b: 2 }).toMatchObject({
  a: expect.any(Number),
  b: expect.any(Number),
})
expect([{ a: 1 }, { b: 2 }]).toEqual([
  expect.objectContaining({ a: expect.any(Number) }),
  expect.anything(),
])
```

### Exceptions

```js
// const fn = () => { throw new Error('Out of cheese!') }
expect(fn).toThrowError()
expect(fn).toThrowError('Out of cheese')
expect(fn).toThrowErrorMatchingSnapshot()
expect(fn).toThrowErrorMatchingInlineSnapshot()

// const fn = () => { console.error('Out of cheese!') }
expect(fn).not.toThrowError()
expect(fn).not.toThrowError('Out of cheese')
```

### Snapshots

```js
expect(node).toMatchSnapshot()
expect(user).toMatchSnapshot({
  date: expect.any(Date),
})
expect(user).toMatchInlineSnapshot()
```

### Misc

```js
expect(new A()).toBeInstanceOf(A)
expect(() => {}).toEqual(expect.any(Function))
expect('pizza').toEqual(expect.anything())
```

### Promise matchers

> [!WARN]  
> Don’t forget to return the result of the `expect()` method from each test case.

```js
test('resolves with a lemon', () => {
  return expect(Promise.resolve('lemon')).resolves.toBe('lemon')
})
test('rejects with an octopus', () => {
  return expect(Promise.reject('octopus')).rejects.toBeDefined()
})
test('rejects with an error', () => {
  return expect(Promise.reject(Error('pizza'))).rejects.toThrow()
})
```

Or with async/await:

> [!WARN]  
> Don’t forget to `await` the `expect()` method in each test case.

```js
test('resolves with a lemon', async () => {
  await expect(Promise.resolve('lemon')).resolves.toBe('lemon')
})
test('doesn’t resolve with an octopus', async () => {
  await expect(Promise.resolve('lemon')).resolves.not.toBe('octopus')
})
```

## Async tests

### async/await

```js
test('async test', async () => {
  const result = await runAsyncOperation()
  expect(result).toBe(true)
})
```

### Promises

_Return_ a Promise from your test:

```js
test('async test', () => {
  return runAsyncOperation().then((result) => {
    expect(result).toBe(true)
  })
})
```

## Mocks

### Mock functions

[Mock functions docs](https://jestjs.io/docs/en/mock-function-api)

```js
import { expect, vi } from 'vitest'

// const fn = vi.fn()
// const fn = vi.fn().mockName('Unicorn') -- named mock, Jest 22+
expect(fn).toHaveBeenCalled() // Function was called
expect(fn).not.toHaveBeenCalled() // Function was *not* called
expect(fn).toHaveBeenCalledTimes(1) // Function was called only once
expect(fn).toHaveBeenCalledWith(arg1, expect.anything(), arg3) // Any of calls was with these arguments
expect(fn).toHaveBeenLastCalledWith(arg1, arg2) // Last call was with these arguments
expect(fn).toHaveBeenNthCalledWith(callNumber, args) // Nth call was with these arguments (Jest 23+)
expect(fn).toHaveReturnedTimes(2) // Function was returned without throwing an error (Jest 23+)
expect(fn).toHaveReturnedWith(value) // Function returned a value (Jest 23+)
expect(fn).toHaveLastReturnedWith(value) // Last function call returned a value (Jest 23+)
expect(fn).toHaveNthReturnedWith(value) // Nth function call returned a value (Jest 23+)

// Multiple calls
expect(fn.mock.calls).toEqual([
  ['first', 'call', 'args'],
  ['second', 'call', 'args'],
])

// fn.mock.calls[0][0] — the first argument of the first call
expect(fn.mock.calls[0][0]).toBe(2)
```

You can also use snapshots:

```js
test('call the callback', () => {
  const callback = vi.fn().mockName('Unicorn') // mockName is available in Jest 22+
  fn(callback)
  expect(callback).toMatchSnapshot()
  // ->
  // [MockFunction Unicorn] {
  //   "calls": Array [
  // ...
})
```

And pass an implementation to `vi.fn` function:

```js
const callback = vi.fn(() => true)
```

### Returning, resolving and rejecting values

Your mocks can return values:

```js
const callback = vi.fn().mockReturnValue(true)
const callbackOnce = vi.fn().mockReturnValueOnce(true)
```

Or resolve values:

```js
const promise = vi.fn().mockResolvedValue(true)
const promiseOnce = vi.fn().mockResolvedValueOnce(true)
```

They can even reject values:

```js
const failedPromise = vi.fn().mockRejectedValue('Error')
const failedPromiseOnce = vi.fn().mockRejectedValueOnce('Error')
```

You can even combine these:

```js
const callback = vi.fn().mockReturnValueOnce(false).mockReturnValue(true)

// ->
//  call 1: false
//  call 2+: true
```

### Mock modules using `vi.mock()` method

[Mock modules](https://vitest.dev/api/mock.html)

```js
import { vi } from 'vitest'

// The original lodash/memoize should exist
vi.mock('lodash/memoize', () => (a) => a)
// The original lodash/memoize isn’t required
vi.mock('lodash/memoize', () => (a) => a, { virtual: true })
```

### Mock modules using a mock file

1.  Create a file like `__mocks__/lodash/memoize.js`:

    ```js
    module.exports = (a) => a
    ```

2.  Add to your test:

    ```js
    vi.mock('lodash/memoize')
    ```

### Mock object methods

```js
import { vi } from 'vitest'

const spy = vi.spyOn(console, 'log').mockImplementation(() => {})
expect(console.log.mock.calls).toEqual([['dope'], ['nope']])
spy.mockRestore()
```

```js
import { vi } from 'vitest'

const spy = vi.spyOn(ajax, 'request').mockImplementation(() => Promise.resolve({ success: true }))
expect(spy).toHaveBeenCalled()
spy.mockRestore()
```

### Mock getters and setters

```js
import { vi } from 'vitest'

const location = {}
const getTitle = vi.spyOn(location, 'title', 'get').mockImplementation(() => 'pizza')
const setTitle = vi.spyOn(location, 'title', 'set').mockImplementation(() => {})
```

### Clearing and restoring mocks

For one mock:

```js
fn.mockClear() // Clears mock usage date (fn.mock.calls, fn.mock.instances)
fn.mockReset() // Clears and removes any mocked return values or implementations
fn.mockRestore() // Resets and restores the initial implementation
```

> [!NOTE]  
> The `mockRestore()` method works only with mocks created by `vi.spyOn()`.

For all mocks:

```js
vi.clearAllMocks()
vi.resetAllMocks()
vi.restoreAllMocks()
```

### Accessing the original module when using mocks

```js
import { vi } from 'vitest'

vi.mock('./example.js', async () => {
  const axios = await vi.importActual('./example.js')

  return { ...axios, get: vi.fn() }
})
```

### Timer mocks

[Fake times](https://vitest.dev/api/vi.html#fake-timers)

Write synchronous test for code that uses native timer functions (`setTimeout`, `setInterval`, `clearTimeout`, `clearInterval`).

```js
// Enable fake timers
vi.useFakeTimers()

test('kill the time', () => {
  const callback = vi.fn()

  // Run some code that uses setTimeout or setInterval
  const actual = someFunctionThatUseTimers(callback)

  // Fast-forward until all timers have been executed
  vi.runAllTimers()

  // Check the results synchronously
  expect(callback).toHaveBeenCalledTimes(1)
})
```

Or adjust timers by time with [vi.advanceTimersByTime()](https://vitest.dev/api/vi.html#vi-advancetimersbytime):

```js
// Enable fake timers
vi.useFakeTimers()

test('kill the time', () => {
  const callback = vi.fn()

  // Run some code that uses setTimeout or setInterval
  const actual = someFunctionThatUseTimers(callback)

  // Fast-forward for 250 ms
  vi.advanceTimersByTime(250)

  // Check the results synchronously
  expect(callback).toHaveBeenCalledTimes(1)
})
```

Use [vi.runOnlyPendingTimers()](https://vitest.dev/api/vi.html#vi-runonlypendingtimers) for special cases.

> ![NOTE]  
> You should call `vi.useFakeTimers()` in your test case to use other fake timer methods.

## Data-driven tests

Run the same test with different data:

```js
test.each([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])('.add(%s, %s)', (a, b, expected) => {
  expect(a + b).toBe(expected)
})
```

Or the same using template literals:

```js
test.each`
  a    | b    | expected
  ${1} | ${1} | ${2}
  ${1} | ${2} | ${3}
  ${2} | ${1} | ${3}
`('returns $expected when $a is added $b', ({ a, b, expected }) => {
  expect(a + b).toBe(expected)
})
```

Or on `describe` level:

```js
describe.each([['mobile'], ['tablet'], ['desktop']])('checkout flow on %s', (viewport) => {
  test('displays success page', () => {
    //
  })
})
```

[describe.each() docs](https://vitest.dev/api/#describe-each), [test.each() docs](https://vitest.dev/api/#test-each),

## Skipping tests

Don’t run these tests:

```js
describe.skip('makePoniesPink'...
tests.skip('make each pony pink'...
```

Run only these tests:

```js
describe.only('makePoniesPink'...
tests.only('make each pony pink'...
```

## Testing modules with side effects

Node.js and Vitest cache modules you `import`. To test modules with side effects you’ll need to reset the module registry between tests:

```js
const modulePath = '../module-to-test'

afterEach(() => {
  vi.resetModules()
})

test('first test', async () => {
  // Prepare conditions for the first test
  const result = await import(modulePath)
  expect(result).toMatchSnapshot()
})

test('second text', async () => {
  // Prepare conditions for the second test
  const fn = () => await import(modulePath)
  expect(fn).toThrowError()
})
```

## Resources

- [Vitest site](https://vitest.dev/)
- [Modern React testing, part 1: best practices](https://blog.sapegin.me/all/react-testing-1-best-practices/) by Artem Sapegin
- [Modern React testing, part 2: Jest and Enzyme](https://blog.sapegin.me/all/react-testing-2-jest-and-enzyme/) by Artem Sapegin
- [Modern React testing, part 3: Jest and React Testing Library](https://blog.sapegin.me/all/react-testing-3-jest-and-react-testing-library/) by Artem Sapegin
- [Modern React testing, part 4: Cypress and Cypress Testing Library](https://sapegin.me/blog/react-testing-4-cypress/) by Artem Sapegin
- [Modern React testing, part 5: Playwright](https://sapegin.me/blog/react-testing-5-playwright/) by Artem Sapegin

---

## Contributing

Improvements are welcome! Open an issue, or send a pull request.

## Sponsoring

This software has been developed with lots of coffee, buy me one more cup to keep it going.

<a href="https://www.buymeacoffee.com/sapegin" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/lato-orange.png" alt="Buy Me A Coffee" height="51" width="217" ></a>

## Author and license

[Artem Sapegin](https://sapegin.me/), a frontend engineer at [Stage+](https://www.stage-plus.com) and the creator of [React Styleguidist](https://react-styleguidist.js.org/). I also write about frontend at [my blog](https://sapegin.me/blog/).

CC0 1.0 Universal license, see the included [License.md](/License.md) file.

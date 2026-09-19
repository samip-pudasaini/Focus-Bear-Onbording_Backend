# Setting up Jest in React project

Install using the npm package manager: `npm install --save-dev jest`

To check if it has it: use `npm list jest`

![jest installation](jest_install.png)

# Creating a simple utility function

```js
//src/utils/add.js

export function add(a, b) {
  return a + b;
}
```

**Test**

```js
//src/utils/add.test.js
import { add } from "./add";

test("adds two numbers correctly", () => {
  expect(add(2, 3)).toBe(5);
});
```

Run test using `npm test`

![test](test_result.png)

Terminal output:

```
PS Z:\NTO\project\RealTimeChat\frontend> npm test

> frontend@0.0.0 test
> node --experimental-vm-modules node_modules/jest/bin/jest.js

(node:27800) ExperimentalWarning: VM Modules is an experimental feature and might change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
 PASS  src/utils/add.test.js
  √ adds two numbers correctly (2 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.526 s
Ran all test suites.
PS Z:\NTO\project\RealTimeChat\frontend>
```

# Reflection

## Why is automated testing important in software development?

Automated testing is important because it allows developers to check that their
code works as expected without having to manually test everything each time.
Tests can be run repeatedly after making changes to the code, which helps detect
bugs and prevents existing functionality from accidentally breaking.

Unit tests are especially useful because they test small, individual parts of an
application, such as a utility function. This makes it easier to identify where
a problem occurs. Automated tests also provide more confidence when modifying or
adding new features because the existing tests can be run to check that the
changes have not introduced unexpected problems.

## What did you find challenging when writing your first Jest test?

The most challenging part of writing my first Jest test was understanding the
structure of the test and how `expect()` and matchers such as `toBe()` are used
to check the result. The actual test was simple once I understood the syntax.

I also had to understand how the test file should import the function being
tested and how Jest discovers test files. After running the test and seeing it
pass, the basic process became clearer.

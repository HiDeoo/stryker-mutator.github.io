---
slug: typescript-support
title: TypeScript support
author: Nico Jansen
author_title: Stryker Team
author_url: https://github.com/nicojs
author_image_url: https://avatars3.githubusercontent.com/u/1828233?s=400&u=fec18ad3776aaafec54c49bbd7173a841ae7ea59&v=4
tags: [stryker-js]
---

Stryker 0.10 marks the biggest change in Stryker history yet, as we now support running mutation testing on TypeScript code.

<!--truncate-->

For the entire changelog per package:

- [stryker changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker/CHANGELOG.md)
- [stryker-api changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-api/CHANGELOG.md)
- [stryker-html-reporter changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-html-reporter/CHANGELOG.md)
- [stryker-typescript changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-typescript/CHANGELOG.md)
- [stryker-mocha-runner changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-mocha-runner/CHANGELOG.md)
- [stryker-mocha-framework changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-mocha-framework/CHANGELOG.md)

## Language agnostic

Stryker is now language agnostic. This means that you can use Stryker to mutation test _any_ language that
transpiles down to JavaScript (as long as you have the correct plugin for it). TypeScript is the first language that is
fully supported in this way.

To make this possible we made a number of changes to the Stryker core:

- Changed the Mutator plugin api
- Add a Transpiler plugin api

### Mutator plugin api

With the previous Mutator plugin, it was possible to mutate a single Abstract Syntax Tree (AST) node. It was a neat little feature,
but was never used outside of the core Stryker package. This plugin was also specific to JavaScript.
As Stryker is now language agnostic, we decided to move the Mutator api to a higher level of abstraction.
A Mutator is now responsible for mutating code written in a specific language, instead of a single JS AST node.

We moved the existing es5 Mutator code into a new Mutator called `'es5'`.
It is at this moment still part of the main Stryker npm module, but we have plans to remove it later on.
We also added a [`'typescript'` mutator](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-typescript/src/TypescriptMutator.ts)
as part of the new stryker-typescript npm module.

You can find the new [api definition here](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-api/src/mutant/Mutator.ts).

### Transpiler plugin api

With a transpiler plugin, it possible to transform any source code before Stryker runs your tests.
It is also used to transpile each mutant in the same way. This allows maximum freedom when
mutating source code, without having to worry about the resulting JavaScript code.
A side effect is that it will not work with [coverage analysis](https://github.com/stryker-mutator/stryker-js/tree/master/packages/stryker#type-of-coverage-analysis) yet,
meaning that Stryker will force coverage analysis to be `'off'` when a transpiler is used.

We created the [`'typescript'` transpiler](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-typescript/src/TypescriptTranspiler.ts)
as part of the new `stryker-typescript` npm module.

You can find the new [api definition here](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-api/src/transpile/Transpiler.ts)

## The TypeScript mutation testing framework

[Original PR](https://github.com/stryker-mutator/stryker-js/pull/376)

Stryker now supports TypeScript. This means that Stryker is now able to work
directly on your TypeScript code, no need to first transpile it yourself and run Stryker on the transpiled code.
This has the following advantages:

- Far less false-positives,
- Your stryker reports will now show your code, instead of transpiled code.

### "There's a plugin for that"

All Stryker plugins to help you with TypeScript are part of a single npm module: [stryker-typescript](https://www.npmjs.com/package/stryker-typescript).
It contains 3 plugins that work together:

1. **Config Editor**: A plugin that reads your tsconfig.json file and makes the config available to both the mutator and transpiler.
1. **Mutator**: A plugin that can mutate TypeScript code. See [our unit tests](https://github.com/stryker-mutator/stryker-js/tree/master/packages/stryker-typescript/test/unit/mutator) to know what mutations are support.
1. **Transpiler**: A plugin that can transpile (mutated) TypeScript code. It uses your tsconfig settings, so the output will be exactly as you'd expect.

To configure the plugins you need to add this to your stryker.conf.js file:

```javascript
module.exports = function (config) {
  config.set({
    // ...
    mutator: 'typescript',
    transpilers: ['typescript'],
    tsconfigFile: 'tsconfig.json',
    // ...
  });
};
```

### Great! But what about performance?

You might be thinking all this has a negative impact on performance.
After all, we have to transpile the TypeScript code for each mutant we want to test.
When running Stryker on itself we notice that it takes less time than before when we were mutating JavaScript.
This is because transpiling each mutant ensures that no false positives are tested. Results for your project may differ.

Take the [BinaryExpressionMutator](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-typescript/src/mutator/BinaryExpressionMutator.ts)
for example. It might mutate:

```typescript
'foo' + 'bar';
```

into:

```typescript
'foo' - 'bar';
```

Although this would be valid in JavaScript, it is _invalid_ in TypeScript.
This means that the mutant will result in a `TranspileError`. It is _not_ tested and _not_ calculated into your mutation score.
Less work means it can be faster.

## What's next?

Next, we want to further improve upon our TypeScript support. For example we want to add support
for [coverage analysis](https://github.com/stryker-mutator/stryker-js/tree/master/packages/stryker#type-of-coverage-analysis)
when using a transpiler. We're also thinking about an es6 mutator using Babel under the hood.

Please let us know what you think.

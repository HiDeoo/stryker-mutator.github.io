---
slug: use-git-to-select-files
title: Use git to select files
author: Nico Jansen
author_title: Stryker Team
author_url: https://github.com/nicojs
author_image_url: https://avatars3.githubusercontent.com/u/1828233?s=400&u=fec18ad3776aaafec54c49bbd7173a841ae7ea59&v=4
tags: [stryker-js]
---

The 0.21 release of Stryker simplifies configuration by using your git database.

<!--truncate-->

For a change log per package:

- [stryker changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker/CHANGELOG.md)
- [stryker-api changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-api/CHANGELOG.md)
- [stryker-mocha-runner changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-mocha-runner/CHANGELOG.md)
- [stryker-karma-runner changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-karma-runner/CHANGELOG.md)
- [stryker-typescript changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-typescript/CHANGELOG.md)
- [stryker-webpack-transpiler changelog](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-webpack-transpiler/CHANGELOG.md)

## Sandboxes

Stryker only works on copies of your source code. A copy is called a _sandbox_. It creates as much of them as you want parallel test runners.
You don't want a big sandbox. Just imagine what would happen if all your node_modules end up in there! That's why Stryker allows you to configure
the files to load into it.

One of the most common issues in [our issue tracker](https://github.com/stryker-mutator/stryker-js/issues) has to do with
the way you needed to configure it using the `files` configuration. It was very confusing. The fact that stryker-karma-runner (pre 0.13) and stryker-typescript (pre 0.10)
modified your files array (without you knowing it) only added to the complexity.
Just [a small look at the angular example file](https://github.com/nicojs/angular-stryker-example/blob/72d85e19657247a77faa8e12587d3d301147b2bd/stryker.conf.js#L6)
will leave you scratching your head.

Further more, you had properties like `included`, `transpiled` and `mutated`. It was kind of a mess really.

## Remove your `files` configuration

No longer! From release 0.21 of Stryker, the files array is _optional_. Please remove it. Everything should work as expected.
Stryker will fallback to a sane default: your git database. It does this by executing the following command:
`git ls-files --others --exclude-standard --cached`. The result should be exactly what you want in your sandbox
(given you're using `.gitignore` files like a boss). If you want to deviate from this for whatever reason,
you can still use the `files` property with an array of strings.

Stryker also stopped keeping track of `included`, `transpiled` and `mutated` properties. For `mutate` please use the
designated [`mutate` top level stryker property](https://github.com/stryker-mutator/stryker-js/tree/master/packages/stryker#source-code-files-to-mutate).

## Breaking change!

Unfortunately we couldn't create this feature without some breaking changes. The biggest one is that we've had to remove the old
`InputFileDescriptor` object way of defining your input file.

This:

```js
// DEPRECATED
files: [{ pattern: 'pattern', included: true, mutated: true, transpiled: true }];
```

Is deprecated. If you are using it we strongly suggest to try to remove `files` entirely.

We've had to update the API to test runner plugins and transpiler plugins as well. Before, Stryker was keeping track of which file
needed to be included in the test runner or transpiled by the transpiler. This is
no longer the case. To see what this means for you, please consult the readme of the plugins you are using:

- [stryker-mocha-runner readme](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-mocha-runner/README.md)
- [stryker-karma-runner readme](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-karma-runner/README.md)
- [stryker-typescript readme](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-typescript/README.md)
- [stryker-webpack-transpiler readme](https://github.com/stryker-mutator/stryker-js/blob/master/packages/stryker-webpack-transpiler/README.md)

## What's next?

With this major refactoring out of the way we will hone in to our next target: an actual v1 release.
We've opened up [issue 654](https://github.com/stryker-mutator/stryker-js/issues/654) to keep track of our
progress on this front. Want to help? Or curious about our plans? Please get in touch.

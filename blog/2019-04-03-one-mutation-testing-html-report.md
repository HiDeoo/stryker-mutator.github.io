---
slug: one-mutation-testing-html-report
title: One mutation testing HTML report
author: Nico Jansen
author_title: Stryker Team
author_url: https://github.com/nicojs
author_image_url: https://avatars3.githubusercontent.com/u/1828233?s=400&u=fec18ad3776aaafec54c49bbd7173a841ae7ea59&v=4
tags: [stryker-js, stryker4s, stryker.net, mutation testing elements]
---

If you've used Stryker before, you'll know that it produces a gorgeous report you can view in the browser.
It works by generating HTML files based on the events raised by Stryker.
During development of Stryker4s and Stryker.NET, we realized that producing the same report would be a lot of
work.

Instead of a new HTML reporter implementation for each Stryker framework, we've decided to move the logic of presenting the report to your browser.
That way, any framework can support an HTML report simply by producing a JSON file.

We call it [mutation testing elements](https://github.com/stryker-mutator/mutation-testing-elements#readme) and
it is implemented using the mature
[web components suite of features](https://www.w3.org/TR/components-intro/).
You can now use it in Stryker, Stryker.NET and Stryker4s

<!--truncate-->

## 👷‍ Usage

To enable the new HTML reporter, simply update to the latest Stryker release and
enable the HTML reporter in the implementation of your choice.

- **Stryker** (since v1.2)  
  Enable by adding `'html'` to your `reporters` in your `stryker.conf.js` file.
- **Stryker4s** (since v0.3.1)  
  Enable by adding `"html"` to your `reporters` in your `stryker4s.conf` file.
- **Stryker.NET** (since v0.9)  
  Enable `"html"` as reporter in your `stryker-config.json` file.

Upgrading should be painless.

## 💸 Show me the money

See two screenshots taken from a mutation testing report of Stryker itself 🎉

![Directory](/images/blogs/mutation-testing-elements-directory.png)

![File](/images/blogs/mutation-testing-elements-file.png)

Mutation testing elements comes with batteries included:

📊 Calculates and displays the mutation score and other metrics  
📁 Group results into directories  
👓 Show mutants directly in your source code  
😎 Highlight code with embedded [highlightjs](https://highlightjs.org/)  
🧙‍ Filter mutants based on the outcome  
🔗 Deep linking using anchors (uses fragment, so path will not be contaminated)  
✨ A nice (yet basic) UI with embedded [bootstrap](https://getbootstrap.com) css  
🎓 Adheres to [custom element best practices](https://developers.google.com/web/fundamentals/web-components/best-practices)

## 💭 How it works

Using mutation testing elements is as simple as loading the JavaScript bundle and adding
the `mutation-test-report-app` to your page, pointing the `src` to a json file that contains the
mutation testing results to be displayed.

```html
<body>
  <script src="https://www.unpkg.com/mutation-testing-elements@1.0.2/dist/mutation-test-elements.js"></script>
  <mutation-test-report-app src="mutation-report.json">
    Your browser does not support custom elements. Please use a modern browser.
  </mutation-test-report-app>
</body>
```

Loading from an external source (using `src="..."`) only works if you serve the HTML file from a server.
Use property binding to also support opening your HTML report from disk directly.

```js
document.querySelector('mutation-test-report-app').report = {
  /*... report data ...*/
};
```

The report json should adhere to the [mutation testing report json schema](https://github.com/stryker-mutator/mutation-testing-elements/tree/master/packages/mutation-testing-report-schema#readme),
we've designed it with this specific goal in mind.

For more information on how to use it, as well as where to find the JavaScript implementation, please see the [readme of mutation testing elements](https://github.com/stryker-mutator/mutation-testing-elements/tree/master/packages/mutation-testing-elements#readme)
on github. Want to contribute or suggest changes? Feel free to do so in the [mutation testing elements mono repo](https://github.com/stryker-mutator/mutation-testing-elements).

## 💪 Call to action

Mutation testing elements is here to help out all mutation testing frameworks.
We've contacted the guys over at [Infection](https://infection.github.io/) (PHP)
and they will [support it in the future](https://github.com/infection/infection/issues/340#issuecomment-477148001).

If your mutation testing framework isn't supported, why not open up an issue at their side.
Or better yet, implement it in a pull request. Most of them are open source after all.
Spread the word 💖 to help us grow 🌳.

## 🔮 What's next?

We're planning to upgrade the [Stryker dashboard](https://dashboard.stryker-mutator.io).
Right now it already supports [showing a mutation score badge](/blog/2018-02-08/get-your-mutation-score-badge-now).
In the future, you will be able to host your entire mutation testing report there. Stay tuned.

Happy mutation testing!

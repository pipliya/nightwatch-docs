---
title: Using CucumberJs with nightwatch
description: Learn how to integrate and use CucumberJs with nightwatch.
---

## Using CucumberJS with Nightwatch

Nightwatch 2 brings integrated support for using [Cucumber.js](https://cucumber.io/) directly as an alternative test runner. No other plugins are necessary, other than the [Cucumber library](https://www.npmjs.com/package/@cucumber/cucumber) itself (version 7.3 or higher).

Simply run the following in the same project where Nightwatch is also installed:

```bash
npm i @cucumber/cucumber --save-dev```

### Configuration

In order to use CucumberJS in Nightwatch you need to set the `test_runner` config property and set the type to `cucumber`. You will also need to set the path to where the feature files are located.

#### Example

```js
{
  test_runner: {
    // set cucumber as the runner
    type: 'cucumber',

    // define cucumber specific options

    options: {
      //set the feature path
      feature_path: 'examples/cucumber-js/*/*.feature',

      // start the webdriver session automatically (enabled by default)
      auto_start_session: true,

      // use parallel execution in Cucumber
      // set number of workers to use (can also be defined in the cli as --parallel 2
      parallel: 2
    }
  },

  src_folders: ['examples/cucumber-js/features/step_definitions']
}```

### Running Tests

The easiest way to run cucumber tests from examples is:

```bash
npx nightwatch --env cucumber-js```

Cucumber spec files/step definition files can be provided in `src_folders` in Nightwatch config or as a CLI argument.

With `src_folders` defined:

```bash
npx nightwatch```

Without `src_folders` defined:

```bash
npx nightwatch examples/cucumber-js/features/step_definition```

#### Running in Parallel

Parallel running using 2 workers:

```bash
nightwatch examples/cucumber-js/features/step_definitions --parallel 2```

Use other [test runner options](https://nightwatchjs.org/guide/running-tests/command-line-options.html) as usual:

```bash
npx nightwatch examples/cucumber-js/features/step_definitions --headless```

### Manually Starting the WebDriver Session

You might need sometimes to not start the Webdriver session automatically after Nightwatch is instantiated. For this purpose, Nightwatch provides the instance available as `this.client`, which contains an `launchBrowser()` method.

#### Configuration

```js
{
  test_runner: {
    type: 'cucumber',
    options: {
      feature_path: 'examples/cucumber-js/*/*.feature',
      auto_start_session: false
    }
  }
}```

You can then use an extra setup file that you can pass as an extra `--require` to Nightwatch, which will be forwarded to Cucumber. In the extra setup file, you can add other operations needed to be executed before the session is started.

#### Example _extra_setup.js

Remember to set the `browser` on `this` so it can be closed automatically by Nightwatch. Otherwise, remember to call `.quit()` in your own Cucumber `After()` hooks.

```js
const {Before} = require('@cucumber/cucumber');

Before(async function(testCase) {
  if (!this.client) {
    console.error('Nightwatch instance was not created.');

    return;
  }

  this.client.updateCapabilities({
    testCap: 'testing'
  });

  this.browser = await this.client.launchBrowser();
});```

#### Run with extra setup

```bash
nightwatch examples/cucumber-js/features/step_definitions --require {/full/path/to/_extra_setup.js}```

#### Nightwatch setup file for Cucumber

You might also want to inspect the built-in setup file that Nightwatch uses for initializing the Cucumber runner. It is available in our project root folder at [/cucumber-js/_setup_cucumber_runner.js](https://github.com/nightwatchjs/nightwatch/blob/v2/cucumber-js/_setup_cucumber_runner.js).

### Reporting

When using the integrated Cucumber test runner, you need to use the Cucumber [formatters](https://github.com/cucumber/cucumber-js/blob/main/docs/formatters.md) for generating output.

<div class="alert alert-warning">
Nightwatch reporters (like JUnit XML reports or the [global custom reporter](https://v2.nightwatchjs.org/guide/extending-nightwatch/custom-reporter.html)) are not available. The main reason is that reporting is delegated to the Cucumber CLI. You can also [write your own](https://github.com/cucumber/cucumber-js/blob/main/docs/custom_formatters.md) Cucumber formatter.
</div>

Nightwatch will forward `--format` and `--format-options` CLI arguments, if present, to Cucumber.

By default, the `progress` formatter is used.

For example:

```bash
npx nightwatch --env cucumber-js --format @cucumber/pretty-formatter```

or:

```bash
npx nightwatch --env cucumber-js --require cucumber.conf.js --format json:report/cucumber_report.json```

#### Example  Output

Here's how the output looks like when running the example tests in Firefox. You can just run this in the project where Nightwatch is installed:

```bash
npx nightwatch examples/cucumber-js/features/step_definition```

```bash
ℹ Connected to GeckoDriver on port 4444 (1740ms).
Using: firefox (92.0.1) on MAC (20.6.0).

..  ✔ Testing if the page title equals 'Rijksmuseum Amsterdam, home of the Dutch masters' (4ms)
.  ✔ Element <#rijksmuseum-app> was visible after 46 milliseconds.
.  ✔ Testing if element <.search-results> contains text 'Operation Night Watch' (1994ms)
...  ✔ Testing if the page title equals 'Rijksmuseum Amsterdam, home of the Dutch masters' (8ms)
.  ✔ Element <#rijksmuseum-app> was visible after 49 milliseconds.
.  ✔ Testing if element <.search-results> contains text 'The Night Watch, Rembrandt van Rijn, 1642' (1427ms)
.

2 scenarios (2 passed)
10 steps (10 passed)
0m13.024s (executing steps: 0m12.998s)

```
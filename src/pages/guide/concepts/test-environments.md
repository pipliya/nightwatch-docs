---
title: Nightwatch Test Environments
description: Learn how to define, manage, & better large-scale projects with multiple test environments in Nightwatch.
summary_image: https://nightwatchjs.org/img/banner.png
---

## Test Environments

### Overview

Nightwatch provides `test environments` to maintain a better structure of configuration for large-scale projects.

You can define multiple sections (environments) under the `test_settings` dictionary so you could overwrite specific values per environment.

<p class="alert alert-info">A "default" environment is required. All the other environments are inheriting from default and can overwrite settings as needed.</p>

An environment inherits all the base settings and all the settings defined under the "default" environment.

### Example

In the example below, there are two environments:

- `default` (always required)
- `integration`

nightwatch.json

```js
 {
  "src_folders": ["./tests"],

  "test_settings" : {
    "default" : {
      "launch_url" : "<http://localhost>",
      "globals" : {
        "myGlobalVar" : "some value",
        "otherGlobal" : "some other value"
      }
    },
    "integration" : {
      "launch_url" : "<http://staging.host>",
      "globals" : {
        "myGlobalVar" : "other value"
      }
    }
  }
}```

<br/>

### Usage

Test environments can be referenced by its key in the command-line test runner, as the  `
--env`
 argument:

```bash
nightwatch --env integration```

This can be useful if you need to have different settings for your local machine and the Continuous Integration server.

### Built-in environments

The configuration file which is auto-generated by Nightwatch (`nightwatch.conf.js`) contains several pre-defined test environments for running tests against several browsers (Firefox, Chrome, Edge, Safari), and also for running tests using Selenium Server or popular cloud testing providers Browserstack and Saucelabs.

Here’s an extract from the `nightwatch.conf.js` config file available as part of the <a href="https://github.com/nightwatchjs/nightwatch-examples/">nightwatch-examples</a> project on Github:

<a href="https://github.com/nightwatchjs/nightwatch-examples/blob/main/nightwatch.conf.js"><i>github.com/nightwatchjs/nightwatch-examples/.../nightwatch.conf.js
</a>

```js module.exports = {
  src_folders: ['tests'],

  test_settings: {
    default: {
      webdriver: {
        start_process: true,
        server_path: ''
      }
    },

    safari: {
      desiredCapabilities : {
        browserName : 'safari',
        alwaysMatch: {
          acceptInsecureCerts: false
        }
      },
      webdriver: {
        port: 4445
      }
    },

    firefox: {
      desiredCapabilities : {
        browserName : 'firefox'
      },

      webdriver: {
        start_process: true,
        port: 4444
      }
    }
  }
}```

### Recommended content

- [Define and use test environments](https://nightwatchjs.org/guide/running-tests/define-test-environments.html)

 <div class="doc-pagination pt-40">
  <div class="previous">
    <a href="/guide/quickstarts/create-and-run-a-test-with-cloud-providers.html">
      <span>←</span>
        <div class="d-flex flex-column">
          <span class="smallT">Back</span>
          <span class="bigT">Run a test with a cloud provider</span>
        </div>
    </a>
  </div>
  <div class="next">
    <a href="/guide/concepts/test-globals.html">
        <div class="d-flex flex-column">
          <span class="smallT">Next Page</span>
          <span class="bigT">Test globals</span>
        </div>
        <span>→</span>
    </a>
  </div>
</div>

## Getting Started

In the project directory, you can run:

```
git clone https://github.com/martinjackson/Getting-Started-With-React.git
cd Getting-Started-With-React/node-example
yarn
yarn start
open your browser to http://localhost:8080
```

## Project Scripts

In the project directory, you can run:

### yarn start
  (runs in parallel `watch:hotload` and `watch:start-api`)
  - Starts the project in development mode.  
  - The page will reload if you make edits.
  - You will also see any lint errors in the console.

  http://localhost:8080 to interact with the application.


### yarn run prod
  (runs `prod:bundle` and then `prod:server`)  
  Preps the app for production by taking all the components in the src directory and creating a single bundle.js. This optimizes for web performance and ease of deployment.
  After the bundle is built, the project is started in production mode.


  http://localhost:80 to interact with the application.



### `npm test`

Launches the test runner in the interactive watch mode.<br>
See the section about [running tests](#running-tests) for more information.



## Integrating with a Node Backend

When running in development mode (`yarn start`), the WebPack HotLoader respondes on port 8080 with all all urls matching '/api/\*' forwarded to localhost:8081 where the NodeJS program will respond.  Should you wish to change this behavior, there is a configuration section in the webpack.config.js file.

```
devServer: {
    host: '0.0.0.0',     // allow more than localhost
    port: 8080,
    contentBase: './public/',

    // allow NodeJS to run side-by-side with webpack-dev-server
    proxy: {  '/api/*': 'http://localhost:8081/' }   // <- backend
 },
```

When running in production mode `yarn run prod`, this projects server/server.js will
respond to port 80 and serve up /public/index.html & bundle.js as well as respond to
API requests with URLs that start with /api/  
*(no source code changes needed ;-)*



## Running Tests

>Note: this feature is available with `react-scripts@0.3.0` and higher.<br>
>[Read the migration guide to learn how to enable it in older projects!](https://github.com/facebookincubator/create-react-app/blob/master/CHANGELOG.md#migrating-from-023-to-030)

Create React App uses [Jest](https://facebook.github.io/jest/) as its test runner. To prepare for this integration, we did a [major revamp](https://facebook.github.io/jest/blog/2016/09/01/jest-15.html) of Jest so if you heard bad things about it years ago, give it another try.

Jest is a Node-based runner. This means that the tests always run in a Node environment and not in a real browser. This lets us enable fast iteration speed and prevent flakiness.

While Jest provides browser globals such as `window` thanks to [jsdom](https://github.com/tmpvar/jsdom), they are only approximations of the real browser behavior. Jest is intended to be used for unit tests of your logic and your components rather than the DOM quirks.

We recommend that you use a separate tool for browser end-to-end tests if you need them. They are beyond the scope of Create React App.

### Filename Conventions

Jest will look for test files with any of the following popular naming conventions:

* Files with `.js` suffix in `__tests__` folders.
* Files with `.test.js` suffix.
* Files with `.spec.js` suffix.

The `.test.js` / `.spec.js` files (or the `__tests__` folders) can be located at any depth under the `src` top level folder.

We recommend to put the test files (or `__tests__` folders) next to the code they are testing so that relative imports appear shorter. For example, if `App.test.js` and `App.js` are in the same folder, the test just needs to `import App from './App'` instead of a long relative path. Colocation also helps find tests more quickly in larger projects.

### Command Line Interface

When you run `npm test`, Jest will launch in the watch mode. Every time you save a file, it will re-run the tests, just like `npm start` recompiles the code.

The watcher includes an interactive command-line interface with the ability to run all tests, or focus on a search pattern. It is designed this way so that you can keep it open and enjoy fast re-runs. You can learn the commands from the “Watch Usage” note that the watcher prints after every run:

![Jest watch mode](http://facebook.github.io/jest/img/blog/15-watch.gif)

### Version Control Integration

By default, when you run `npm test`, Jest will only run the tests related to files changed since the last commit. This is an optimization designed to make your tests runs fast regardless of how many tests you have. However it assumes that you don’t often commit the code that doesn’t pass the tests.

Jest will always explicitly mention that it only ran tests related to the files changed since the last commit. You can also press `a` in the watch mode to force Jest to run all tests.

Jest will always run all tests on a [continuous integration](#continuous-integration) server or if the project is not inside a Git or Mercurial repository.

### Writing Tests

To create tests, add `it()` (or `test()`) blocks with the name of the test and its code. You may optionally wrap them in `describe()` blocks for logical grouping but this is neither required nor recommended.

Jest provides a built-in `expect()` global function for making assertions. A basic test could look like this:

```js
import sum from './sum';

it('sums numbers', () => {
  expect(sum(1, 2)).toEqual(3);
  expect(sum(2, 2)).toEqual(4);
});
```

All `expect()` matchers supported by Jest are [extensively documented here](http://facebook.github.io/jest/docs/api.html#expect-value).<br>
You can also use [`jest.fn()` and `expect(fn).toBeCalled()`](http://facebook.github.io/jest/docs/api.html#tobecalled) to create “spies” or mock functions.

### Testing Components

There is a broad spectrum of component testing techniques. They range from a “smoke test” verifying that a component renders without throwing, to shallow rendering and testing some of the output, to full rendering and testing component lifecycle and state changes.

Different projects choose different testing tradeoffs based on how often components change, and how much logic they contain. If you haven’t decided on a testing strategy yet, we recommend that you start with creating simple smoke tests for your components:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
});
```

This test mounts a component and makes sure that it didn’t throw during rendering. Tests like this provide a lot value with very little effort so they are great as a starting point, and this is the test you will find in `src/App.test.js`.

When you encounter bugs caused by changing components, you will gain a deeper insight into which parts of them are worth testing in your application. This might be a good time to introduce more specific tests asserting specific expected output or behavior.

If you’d like to test components in isolation from the child components they render, we recommend using [`shallow()` rendering API](http://airbnb.io/enzyme/docs/api/shallow.html) from [Enzyme](http://airbnb.io/enzyme/). You can write a smoke test with it too:

```sh
npm install --save-dev enzyme react-addons-test-utils
```

```js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders without crashing', () => {
  shallow(<App />);
});
```

Unlike the previous smoke test using `ReactDOM.render()`, this test only renders `<App>` and doesn’t go deeper. For example, even if `<App>` itself renders a `<Button>` that throws, this test will pass. Shallow rendering is great for isolated unit tests, but you may still want to create some full rendering tests to ensure the components integrate correctly. Enzyme supports [full rendering with `mount()`](http://airbnb.io/enzyme/docs/api/mount.html), and you can also use it for testing state changes and component lifecycle.

You can read the [Enzyme documentation](http://airbnb.io/enzyme/) for more testing techniques. Enzyme documentation uses Chai and Sinon for assertions but you don’t have to use them because Jest provides built-in `expect()` and `jest.fn()` for spies.

Here is an example from Enzyme documentation that asserts specific output, rewritten to use Jest matchers:

```js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders welcome message', () => {
  const wrapper = shallow(<App />);
  const welcome = <h2>Welcome to React</h2>;
  // expect(wrapper.contains(welcome)).to.equal(true);
  expect(wrapper.contains(welcome)).toEqual(true);
});
```

All Jest matchers are [extensively documented here](http://facebook.github.io/jest/docs/api.html#expect-value).<br>
Nevertheless you can use a third-party assertion library like [Chai](http://chaijs.com/) if you want to, as described below.

### Using Third Party Assertion Libraries

We recommend that you use `expect()` for assertions and `jest.fn()` for spies. If you are having issues with them please [file those against Jest](https://github.com/facebook/jest/issues/new), and we’ll fix them. We intend to keep making them better for React, supporting, for example, [pretty-printing React elements as JSX](https://github.com/facebook/jest/pull/1566).

However, if you are used to other libraries, such as [Chai](http://chaijs.com/) and [Sinon](http://sinonjs.org/), or if you have existing code using them that you’d like to port over, you can import them normally like this:

```js
import sinon from 'sinon';
import { expect } from 'chai';
```

and then use them in your tests like you normally do.

### Initializing Test Environment

>Note: this feature is available with `react-scripts@0.4.0` and higher.

If your app uses a browser API that you need to mock in your tests or if you just need a global setup before running your tests, add a `src/setupTests.js` to your project. It will be automatically executed before running your tests.

For example:

#### `src/setupTests.js`
```js
const localStorageMock = {
  getItem: jest.fn(),
  setItem: jest.fn(),
  clear: jest.fn()
};
global.localStorage = localStorageMock
```

### Focusing and Excluding Tests

You can replace `it()` with `xit()` to temporarily exclude a test from being executed.<br>
Similarly, `fit()` lets you focus on a specific test without running any other tests.

### Coverage Reporting

Jest has an integrated coverage reporter that works well with ES6 and requires no configuration.<br>
Run `npm test -- --coverage` (note extra `--` in the middle) to include a coverage report like this:

![coverage report](http://i.imgur.com/5bFhnTS.png)

Note that tests run much slower with coverage so it is recommended to run it separately from your normal workflow.

### Continuous Integration

By default `npm test` runs the watcher with interactive CLI. However, you can force it to run tests once and finish the process by setting an environment variable called `CI`.

When creating a build of your application with `npm run build` linter warnings are not checked by default. Like `npm test`, you can force the build to perform a linter warning check by setting the environment variable `CI`. If any warnings are encountered then the build fails.

Popular CI servers already set the environment variable `CI` by default but you can do this yourself too:

### On CI servers
#### Travis CI

1. Following the [Travis Getting started](https://docs.travis-ci.com/user/getting-started/) guide for syncing your GitHub repository with Travis.  You may need to initialize some settings manually in your [profile](https://travis-ci.org/profile) page.
1. Add a `.travis.yml` file to your git repository.
```
language: node_js
node_js:
  - 4
  - 6
cache:
  directories:
    - node_modules
script:
  - npm test
  - npm run build
```
1. Trigger your first build with a git push.
1. [Customize your Travis CI Build](https://docs.travis-ci.com/user/customizing-the-build/) if needed.

### On your own environment
##### Windows (cmd.exe)

```cmd
set CI=true&&npm test
```

```cmd
set CI=true&&npm run build
```

(Note: the lack of whitespace is intentional.)

##### Linux, OS X (Bash)

```bash
CI=true npm test
```

```bash
CI=true npm run build
```

The test command will force Jest to run tests once instead of launching the watcher.
The build command will check for linter warnings and fail if any are found.

### Disabling jsdom

By default, the `package.json` of the generated project looks like this:

```js
  // ...
  "scripts": {
    // ...
    "test": "react-scripts test --env=jsdom"
  }
```

If you know that none of your tests depend on [jsdom](https://github.com/tmpvar/jsdom), you can safely remove `--env=jsdom`, and your tests will run faster.<br>
To help you make up your mind, here is a list of APIs that **need jsdom**:

* Any browser globals like `window` and `document`
* [`ReactDOM.render()`](https://facebook.github.io/react/docs/top-level-api.html#reactdom.render)
* [`TestUtils.renderIntoDocument()`](https://facebook.github.io/react/docs/test-utils.html#renderintodocument) ([a shortcut](https://github.com/facebook/react/blob/34761cf9a252964abfaab6faf74d473ad95d1f21/src/test/ReactTestUtils.js#L83-L91) for the above)
* [`mount()`](http://airbnb.io/enzyme/docs/api/mount.html) in [Enzyme](http://airbnb.io/enzyme/index.html)

In contrast, **jsdom is not needed** for the following APIs:

* [`TestUtils.createRenderer()`](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering) (shallow rendering)
* [`shallow()`](http://airbnb.io/enzyme/docs/api/shallow.html) in [Enzyme](http://airbnb.io/enzyme/index.html)

Finally, jsdom is also not needed for [snapshot testing](http://facebook.github.io/jest/blog/2016/07/27/jest-14.html). Longer term, this is the direction we are interested in exploring, but snapshot testing is [not fully baked yet](https://github.com/facebookincubator/create-react-app/issues/372) so we don’t officially encourage its usage yet.

### Experimental Snapshot Testing

Snapshot testing is a new feature of Jest that automatically generates text snapshots of your components and saves them on the disk so if the UI output changes, you get notified without manually writing any assertions on the component output.

This feature is experimental and still [has major usage issues](https://github.com/facebookincubator/create-react-app/issues/372) so we only encourage you to use it if you like experimental technology. We intend to gradually improve it over time and eventually offer it as the default solution for testing React components, but this will take time. [Read more about snapshot testing.](http://facebook.github.io/jest/blog/2016/07/27/jest-14.html)

### Editor Integration

If you use [Visual Studio Code](https://code.visualstudio.com), there is a [Jest extension](https://github.com/orta/vscode-jest) which works with Create React App out of the box. This provides a lot of IDE-like features while using a text editor: showing the status of a test run with potential fail messages inline, starting and stopping the watcher automatically, and offering one-click snapshot updates.

![VS Code Jest Preview](https://cloud.githubusercontent.com/assets/49038/20795349/a032308a-b7c8-11e6-9b34-7eeac781003f.png)

## Developing Components in Isolation

Usually, in an app, you have a lot of UI components, and each of them has many different states.
For an example, a simple button component could have following states:

* With a text label.
* With an emoji.
* In the disabled mode.

Usually, it’s hard to see these states without running a sample app or some examples.

Create React App doesn't include any tools for this by default, but you can easily add [React Storybook](https://github.com/kadirahq/react-storybook) to your project. **It is a third-party tool that lets you develop components and see all their states in isolation from your app**.

![React Storybook Demo](http://i.imgur.com/7CIAWpB.gif)

You can also deploy your Storybook as a static app. This way, everyone in your team can view and review different states of UI components without starting a backend server or creating an account in your app.

**Here’s how to setup your app with Storybook:**

First, install the following npm package globally:

```sh
npm install -g getstorybook
```

Then, run the following command inside your app’s directory:

```sh
getstorybook
```

After that, follow the instructions on the screen.

Learn more about React Storybook:

* Screencast: [Getting Started with React Storybook](https://egghead.io/lessons/react-getting-started-with-react-storybook)
* [GitHub Repo](https://github.com/kadirahq/react-storybook)
* [Documentation](https://getstorybook.io/docs)
* [Snapshot Testing](https://github.com/kadirahq/storyshots) with React Storybook

## Making a Progressive Web App

You can turn your React app into a [Progressive Web App](https://developers.google.com/web/progressive-web-apps/) by following the steps in [this repository](https://github.com/jeffposnick/create-react-pwa).

## Deployment

`npm run build` creates a `build` directory with a production build of your app. Set up your favourite HTTP server so that a visitor to your site is served `index.html`, and requests to static paths like `/static/js/main.<hash>.js` are served with the contents of the `/static/js/main.<hash>.js` file. For example, Python contains a built-in HTTP server that can serve static files:

```sh
cd build
python -m SimpleHTTPServer 9000
```

If you're using [Node](https://nodejs.org/) and [Express](http://expressjs.com/) as a server, it might look like this:

```javascript
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static('./build'));

app.get('/', function (req, res) {
  res.sendFile(path.join(__dirname, './build', 'index.html'));
});

app.listen(9000);
```

Create React App is not opinionated about your choice of web server. Any static file server will do. The `build` folder with static assets is the only output produced by Create React App.

However this is not quite enough if you use client-side routing. Read the next section if you want to support URLs like `/todos/42` in your single-page app.

### Serving Apps with Client-Side Routing

If you use routers that use the HTML5 [`pushState` history API](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Adding_and_modifying_history_entries) under the hood (for example, [React Router](https://github.com/ReactTraining/react-router) with `browserHistory`), many static file servers will fail. For example, if you used React Router with a route for `/todos/42`, the development server will respond to `localhost:3000/todos/42` properly, but an Express serving a production build as above will not.

This is because when there is a fresh page load for a `/todos/42`, the server looks for the file `build/todos/42` and does not find it. The server needs to be configured to respond to a request to `/todos/42` by serving `index.html`. For example, we can amend our Express example above to serve `index.html` for any unknown paths:

```diff
 app.use(express.static('./build'));

-app.get('/', function (req, res) {
+app.get('/*', function (req, res) {
   res.sendFile(path.join(__dirname, './build', 'index.html'));
 });
```

Now requests to `/todos/42` will be handled correctly both in development and in production.

### Building for Relative Paths

By default, Create React App produces a build assuming your app is hosted at the server root.<br>
To override this, specify the `homepage` in your `package.json`, for example:

```js
  "homepage": "http://mywebsite.com/relativepath",
```

This will let Create React App correctly infer the root path to use in the generated HTML file.




## Troubleshooting

### `npm test` hangs on macOS Sierra

If you run `npm test` and the console gets stuck after printing `react-scripts test --env=jsdom` to the console there might be a problem with your [Watchman](https://facebook.github.io/watchman/) installation as described in [facebookincubator/create-react-app#713](https://github.com/facebookincubator/create-react-app/issues/713).

We recommend deleting `node_modules` in your project and running `npm install` (or `yarn` if you use it) first. If it doesn't help, you can try one of the numerous workarounds mentioned in these issues:

* [facebook/jest#1767](https://github.com/facebook/jest/issues/1767)
* [facebook/watchman#358](https://github.com/facebook/watchman/issues/358)
* [ember-cli/ember-cli#6259](https://github.com/ember-cli/ember-cli/issues/6259)

It is reported that installing Watchman 4.7.0 or newer fixes the issue. If you use [Homebrew](http://brew.sh/), you can run these commands to update it:

```
watchman shutdown-server
brew update
brew reinstall watchman
```

You can find [other installation methods](https://facebook.github.io/watchman/docs/install.html#build-install) on the Watchman documentation page.

If this still doesn't help, try running `launchctl unload -F ~/Library/LaunchAgents/com.github.facebook.watchman.plist`.

There are also reports that *uninstalling* Watchman fixes the issue. So if nothing else helps, remove it from your system and try again.

### `npm run build` silently fails

It is reported that `npm run build` can fail on machines with no swap space, which is common in cloud environments. If [the symptoms are matching](https://github.com/facebookincubator/create-react-app/issues/1133#issuecomment-264612171), consider adding some swap space to the machine you’re building on, or build the project locally.

## Something Missing?

If you have ideas for more “How To” recipes that should be on this page, [let us know](https://github.com/facebookincubator/create-react-app/issues) or [contribute some!](https://github.com/facebookincubator/create-react-app/edit/master/packages/react-scripts/template/README.md)

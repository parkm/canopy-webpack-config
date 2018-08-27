# canopy-webpack-config
Some defaults for webpack configs at Canopy.

## Goals
- Ensure webpack externals for common dependencies are correct. This ensures smaller bundle sizes.
- Provide some defaults for people implementing sofe services.
- Still be 100% overrideable.

## Things we don't want
- Hiding webpack configs at a level comparable to create-react-app or angular-cli. Nothing will need to be "ejected," you can always override anything you want.
- Discouraging people from experimenting with their webpack config.
- Discouraging people from knowing webpack.
- Being overly opininated about how you do things like css (or other "autonomy" areas of building a sofe service).

## Usage
First, install the library as a devDependency.
```bash
yarn add --dev canopy-webpack-config
```

Now create a webpack.config.js file. This file will use canopy-webpack-config and allow you to override anything you want to about the defaults. Inside of the
file, call the `canopyWebpackConfig()` function to get some defaults and add/override to your webpack config. See example below:

```js
// webpack.config.js

const canopyWebpackConfig = require('canopy-webpack-config');

module.exports = canopyWebpackConfig('login-ui', {
  // Override or add anything you want to your webpack config
  module: {
    rules: [
      { loader: 'css-loader' },
    ],
  },
})
```

Finally, create the following `scripts` in your package.json:

```json
{
  "scripts": {
    "build": "webpack --config webpack.config.js",
    "analyze": "webpack --config webpack.config.js --env.analyze",
    "start": "webpack-dev-server --config ./webpack.config.js --https --disable-host-check --cert ~/.canopy-ssl/public.pem --key ~/.canopy-ssl/key.pem --port"
  }
}
```

Now add `yarn build` to your `.gitlab-ci.yml` file for the build step. You can run `yarn analyze` at any time to see your bundle size and breakdown. And `yarn start`
will start up a web server that is ready to go as a sofe override.

## API
- `canopyWebpackConfig(name, config)`: The default export of the canopy-webpack-config npm library. This function requires both of its arguments.
  The first argument is a string name for the library you are exporting. The second is a webpack config that will be merged with the defaults that
  canopy-webpack-config provides. The library will put the bundled files into the "build" directory. Note that this project assumes that you use
  babel-loader, but it does not install babel-loader. So you'll have to install it yourself, at whatever version you prefer.

## Debugging
To see your full webpack config, simply add a `console.log(module.exports)` inside of your webpack config.

## Features and assumptions
canopy-webpack-config assumes a few things about your project and provides defaults for those things:
- It automatically uses babel-loader to compile js files.
- It compiles your library to AMD format
- It compiles your code into the `build` directory relative to where you started the webpack process.
- It excludes all [common dependencies](git@code.canopy.ninja:front-end/gold/common-dependencies.git) from the build.

## Limitations
the webpack config for canopy-webpack-config will always create the output bundle in the directory that the webpack process was started in. This
is different than how webpack configs normally work -- they usually create the output bundle in relation to the directory in which the webpack config
file is placed.

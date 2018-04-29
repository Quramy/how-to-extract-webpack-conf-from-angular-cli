# HowToExtractWebpackConfFromAngularCli

I generated this project to explain "how to extract webpack configuration object from @angular/cli project?". In addition, I do this without `ng eject` :)

## How to ?

```js
const fs = require('fs');
const path = require('path');

// At the first, we read angular.json (not .angulr-cli.json)
// and extract configration for the target project.
// In most cases we can use key, `defaultProject`.
const angularJson = JSON.parse(fs.readFileSync(path.resolve(__dirname, 'angular.json'), 'utf8'))
const project = angularJson['projects'][angularJson['defaultProject']];

// Create an object to configure webpack using the `buildOptions`.
// And the full schema of this object is described at
// https://github.com/angular/devkit/blob/v6.0.0-rc.5/packages/angular_devkit/build_angular/src/angular-cli-files/models/build-options.ts#L67
const webpackConfigOptions = {
  root: project.root,
  projectRoot: __dirname,
  supportES2015: false,
  tsConfig: {
    options: { },
    fileNames: [],
    errors: [],
  },
  tsConfigPath: path.resolve(__dirname, 'src/tsconfig.app.json'),
  buildOptions: project.architect.build.options,
};

// For now, webpack configulaters are located at the folloing:
const {
  getCommonConfig,
  // getStylesConfig,
  // getAtoConfig, etc...
} = require('@angular-devkit/build-angular/src/angular-cli-files/models/webpack-configs');


// The created configuration object is compatible standard Webpack configuration object.
// So, it has `entry`, `plugins` (and so on) keys.
const webpackCommonConfig = getCommonConfig(webpackConfigOptions);
console.log(webpackCommonConfig);
```

## @angular/cli version
>= 6.0.0-rc.

**We can't this technique with @angular/cli(<= v.5)** because the CLI config files are not compatible.

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 6.0.0-rc.7.

# grunt-ng-constant

> Plugin for dynamic generation of angular constant modules.

## Getting Started
This plugin requires Grunt `~0.4.1`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-ng-constant --save-dev
```

One the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-ng-constant');
```

## The "ngconstant" task

### Overview
In your project's Gruntfile, add a section named `ngconstant` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  ngconstant: {
    options: {
      // Task-specific options go here.
    },
    your_target: {
      // Target-specific module configurations.
    }
  }
});
```

### Options

#### options.space
Type: `String`
Default value: `'\t'`

A string that defines how the `JSON.stringify` method will prettify your code. You can get more information in the [MDN Documentation](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify).

#### options.deps
Type: `Array` or `Boolean`
Default value: `[]`

An array that specifies the default dependencies a module should have. When your module should not have any modules, so you can append the constants to an already existing one, you can set `deps` to `false`.

#### options.wrap
Type: `String` or `Boolean`
Default value: `false`
Optional

A boolean to active or deactive the automatic wrapping. A string who will wrap the result of file, use the `__ngModule` variable to indicate the content.


### Usage Examples

#### Default Options
In this example I convert the package.json information in an angular module. So I am able to display e.g. the current version of the application in the app.

```js
grunt.initConfig({
  ngconstant: {
    dist: {
      dest: 'dist/constants.js',
      name: 'constants',
      constants: {
        package: grunt.file.readJSON('package.json')
      }
    }
  }
});
```

#### Custom Options
In this example we set custom configurations for the `space` and `deps` parameter. So we create a module that has `dep1` and `dep2` as dependency and defines to different constants `constants1` and `constants2` with custom values. The `space` parameter is set to a ` `.

```js
grunt.initConfig({
  ngconstant: {
    options: {
      space: ' ',
      deps: ['dep1', 'dep2']
    },
    dist: {
      dest: 'dist/module.js',
      name: 'someModule',
      constants: {
        'constant1': 'some value you want to set as constant value. This can be of any type that can be transformed via JSON.stringify',
        'constant2': {
          'key1': 'value1',
          'key2': 42
        }
      }
    }
  }
});
```

The resulting module looks like the following:

```
angular.module("someModule", ["dep1", "dep2"])

.constant("constant1", "some value you want to set as constant value.
  This can be of any type that can be transformed via JSON.stringify")

.constant("constant2", {
  "key1": "value1",
  "key2": 42
})

;
```

#### Wrap Option

The `wrap` option allows you to encapsulate the module in a closure. Simply set `wrap` to `true`.

```js
grunt.initConfig({
  ngconstant: {
    options: {
      space: ' ',
      deps: ['dep1', 'dep2']
    },
    dist: {
      dest: 'tmp/wrap_options.js',
      name: 'module2',
      deps: ['test'],
      wrap: true,
      constants: {
        'constant1': {
          key1: 123,
          key2: 'value2',
          foobar: false
        }
      }
    }
  },
})
```

The resulting module looks like:

```js
(function(angular, undefined) {
   angular.module("module2", ["test"])

.constant("constant1", {
  "key1": 123,
  "key2": "value2",
  "foobar": false
})

; 
})(angular);
```

#### Custom Wrap Option

If you want to use another wrapping you can use a string as `wrap` option, which is interpolated by the plugin. Use the `__ngModule` variable as placeholder for the generated module.

Here a RequireJS example:

```js
grunt.initConfig({
  ngconstant: {
    options: {
      space: ' ',
      deps: ['dep1', 'dep2']
    },
    dist: {
      dest: 'tmp/wrap_options.js',
      name: 'module2',
      deps: ['test'],
      wrap: 'define( ["angular", "ngResource", "ngCookies"], function() { \n return <%= __ngModule %> \n\n});',
      constants: {
        'constant1': {
          key1: 123,
          key2: 'value2',
          foobar: false
        }
      }
    }
  },
})
```

The resulting module looks like the following:

```
define( ["angular", "ngResource", "ngCookies"], function() { 
 return angular.module("module2", ["test"])

.constant("constant1", {
  "key1": 123,
  "key2": "value2",
  "foobar": false
})

; 

});
```

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).

## Release History

 * v0.4.2 - Wrap option added. Thanks to [gabrielmancini](https://github.com/gabrielmancini).

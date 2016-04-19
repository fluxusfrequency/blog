# Building a Single-Page App With SystemJS

- Supports module loading from anywhere - ES6, AMD, RequireJS
- Works in browser and in Node
- Supports map, paths, bundles, shims
- Works with Babel and Traceur

## Setup

### Adding systemjs

Put it into bower.json

```javascript

{
  "name": "myApp dependencies",
  "dependencies": {
    "system.js": "0.19.17",
    "angular": "~1.5.3",
    // etc
  },
  "overrides": {
    "system.js": "dist/systemjs"
  }
}

```

Gulp task

```javascript
var gulp = require('gulp');
var mainBowerFiles = require('main-bower-files');
var babel = require('gulp-babel');

gulp.task('bower', function() {
  return gulp.src(mainBowerFiles('**/*.js', { base: 'bower_components' })
    .pipe(concat('dependencies.js'))
    .pipe(gulp.dest('public/assets'));
});

gulp.task('js', function() {
  return gulp.src(['app/assets/javascripts/**/*.js'])
    .pipe(babel({
      modules: 'system',
      moduleIds: true
    })
    .pipe(concat('application.js'))
    .pipe(gulp.dest('public/assets'));
});

gulp.task('build', ['bower', 'js'], function() {
  return gulp.src([
    'public/assets/dependencies.js',
    'public/assets/application.js',
    'config.js'
  ])
    .pipe(concat('my-app.js'))
    .pipe(gulp.dest('public/assets'));
});
```

### Setting up config.js

system.config
Transpiler
JS Extensions

Map
map module names to paths
add a line for the app: `"myApp": "app.module"`

### Loading up Dependencies

Bower components
Gulp bower_components / [main-bower-files](https://www.npmjs.com/package/main-bower-files) => public/dependencies.js
Gulp build - concat application.js with dependencies.js


### Bootstrapping the App

```javascript

// app.module.js

import myModule from 'something.in.the.map';
import depModule from 'some.other.library';

let moduleName = 'myApp';

angular.module(moduleName, [
  // dependencies
])
.value('foo', 'bar')
.run([
  // dependencies, (deps) => {
  }
])

// Some other code

export default moduleName;

```

```javascript

// config.js

System.import('myApp').then(function() {
  angular.element(Document).ready(function() {
    angular.bootstrap(document.getElementById('app-container'), ['myApp']);
  });
});

```

## Workflow

Export es6 modules
Add to map


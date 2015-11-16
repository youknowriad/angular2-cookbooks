# How to bootstrap an Angular2 application ?

## Prerequisites

 - none

## Angular version

- 2.0.0-alpha.46

## How to ?

A basic angular2 application is basically composed of three files :

* an index.html where we include the angular2 bundle,
a module loader (systemJS for now) and the root component of our application.

```html
<!-- index.html -->
<!doctype html>
<html>
<head>
  <title>App</title>
  <base href=".">
  <script src="system.js"></script>
  <script src="angular2.dev.js"></script>
  <script>
    System.config({
      packages: {
        app: {
          format: 'register',
          defaultExtension: 'js'
        }
      }
    });
    System.import('bootstrap.js').then(null, console.error.bind(console));
  </script>
</head>
<body>
  <app></app>
</body>
</html>
```

 * our main components file

```javascript
// app.ts compiled (using typescript) to app.js
import {Component, View} from 'angular2/angular2';

@Component({
  selector: 'app'
})
@View({
  template: `
    <h1>Hello world</h1>
  `
})
export class App {
  constructor() {}
}
```

 * and a bootstrap script which is basically a call to the angular2 `bootstrap` function with the root component as parameter.

```javascript
// bootstrap.ts compiled (using typescript) to bootstrap.js
import {bootstrap} from 'angular2/angular2';
import {App} from './app';

bootstrap(App);
```

Linking all this stuff together can be a little tricky for now (dependencies, transpilation, module loader etc...).
The angular team is working on a tool called [angular-cli](https://github.com/angular/angular-cli) that should make boostraping a breeze.

### Using angular-cli

First install the tool like so `npm install -g angular-cli` then you should be able to run the `ng` command.

```bash
ng --help
```

Then, you can bootstrap your application.

```bash
ng new my_app
```

Your application is now ready. By default

You can run a development server like so

```bash
cd my_app
ng serve
```

your application should be available on `http://localhost:4200/`

## Plunker

http://plnkr.co/edit/XliE3SlGIlb25xBXbcfr?p=preview

## Learn more

You can check the following links for other bootstraping methods.

* [Angular 2 Seed](http://mgechev.github.io/angular2-seed#awesome-angular2)
* [Angular 2 Webpack Starter](https://angularclass.com/angular2-webpack-starter#awesome-angular2)

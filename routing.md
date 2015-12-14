# How to use the angular2 router ?

In this simple cookbook, we are going to see how to use the angular2 router,
configure routes and create links between routes.

## Prerequisites

 * [Bootstrap an Angular2 application](bootstrap.md)

## Angular version

- 2.0.0-alpha.53

## How To ?

First we have to include the angular2 router bundle to our application.
The router component is not included by default in the angular2 bundle.

```html
<!-- index.html -->
<!doctype html>
<html>
<head>
  <title>App</title>
  <base href=".">
  <script src="system.js"></script>
  <script src="angular2.dev.js"></script>
  <script src="angular2.router.dev.js"></script>
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

Now we add angular router provider to our global providers

```javascript
// bootstrap.ts compiled (using typescript) to bootstrap.js
import {bootstrap} from 'angular2/platform/browser';
import {App} from './app';
import {ROUTER_PROVIDERS} from 'angular2/router';

bootstrap(App, [ROUTER_PROVIDERS]);
```

Next, let's define some routes in our root component `App`.

```javascript
// app.ts compiled (using typescript) to app.js
import {Component} from 'angular2/core';
import {RouteConfig, ROUTER_DIRECTIVES} from 'angular2/router';
import {Home} from './home';
import {About} from './about';

@Component({
  selector: 'app',
  directives: [ROUTER_DIRECTIVES]
  template: `
    <h1>Hello world</h1>

    <router-outlet></router-outlet>
  `
})
@RouteConfig([
    { path: '/', redirectTo: ['Home'] },
    { path: '/home', as: 'Home', component: Home },
    { path: '/about', as: 'About', component: About }
])
export class App {
  constructor() {}
}
```

So we define routes using the `RouteConfig` decorator.
We pass it an array of plain javascript objects, with a path,
an alias and the component to display for each route.

If the url matchs a path in a route config, the component of this route is injected
in the special directive router-outlet that should be appended
somewhere in the component template.

Now let's create the home and app components (our "pages"):

```javascript
// home.ts
import {Component} from 'angular2/cpre';
import {ROUTER_DIRECTIVES} from 'angular2/router';

@Component({
  selector: 'home',
  directives: [ROUTER_DIRECTIVES]
  template: `
    <h2>Home</h2>

    <a [routerLink]="['../About']">Go to about</a>
  `
})
export class Home {
  constructor() {}
}
```

```javascript
// about.ts
import {Component} from 'angular2/core';
import {ROUTER_DIRECTIVES} from 'angular2/router';

@Component({
  selector: 'about',
  directives: [ROUTER_DIRECTIVES]
  template: `
    <h2>About</h2>

    <a [routerLink]="['../Home']">Go to home</a>
  `
})
export class About {
  constructor() {}
}
```

You may have noticed here, the use of the router-link directive
to navigate between routes.

## Plunker

http://plnkr.co/edit/vq5gYTN1XqqaPkI3LhoN?p=preview

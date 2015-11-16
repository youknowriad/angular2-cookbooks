# How to create an Angular2 service ?

In this cookbook, we're going to see how to declare an Angular2 service
and how to use it in an Angular2 component or in another service

## Prerequisites

 * [Bootstrap an Angular2 application](bootstrap.md)

## Angular version

- 2.0.0-alpha.46

## How To ?

A service in Angular2 is just a class that will be instantiated by the angular DI (Dependency Injection) when needed.
Let's we create a service to slugify strings. It would look like this.

```javascript
// slugifyer.ts
import {Injectable} from 'angular2/angular2';

@Injectable()
export class Slugifyer {
  slugify(str: String) {
    return str.toString().toLowerCase()
      .replace(/\s+/g, '-')     // Replace spaces with -
      .replace(/[^\w\-]+/g, '') // Remove all non-word chars
      .replace(/\-\-+/g, '-')   // Replace multiple - with single -
      .replace(/^-+/, '')       // Trim - from start of text
      .replace(/-+$/, '');
  }
}
```

You may have noticed the `Injectable` annotation. This annotation does nothing special. It's only here to tell typescript to export the class metadata used by the Angular2 DI.

All we have to do next is tell Angular2 how to "provide" the service slugifyer to any component or service that needs it. The simple way to do this is to pass the class `Slugigyer` as a provider in the Angular2 bootstrap call.

```javascript
// bootstrap.ts compiled (using typescript) to bootstrap.js
import {bootstrap} from 'angular2/angular2';
import {App} from './app';

import {Slugifyer} from './slugifyer';

bootstrap(App, [
  Slugifyer
]);
```

We can now inject the service in any angular2 components or service as a constructor dependency. Here is an example :

```javascript
// app.ts
import {Component, View} from 'angular2/angular2';
import {Slugifyer} from './slugifyer';

@Component({
  selector: 'app'
})
@View({
  template: `
    <h1>{{ slug }}</h1>
  `
})
export class App {
  slug: string;
  constructor(slugifyer: Slugifyer) {
    this.slug = slugifyer.slugify('My plain text');
  }
}
```

## Plunker

http://plnkr.co/edit/lhRPv1zqeIswGte2Ti5D?p=preview

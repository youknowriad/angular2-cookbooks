# How to create an Angular2 Pipe ?

Pipes in Angular2 are the equivalent of filters in Angular.JS (1). Let's create a simple Pipe to truncate strings.

## Prerequisites

 * [Bootstrap an Angular2 application](bootstrap.md)

## Angular version

- 2.0.0-alpha.53

## How To ?

A pipe in Angular2 is just a class with a transform method like so

```javascript
// truncate.ts
import {Pipe} from 'angular2/core'

@Pipe({
  name: 'truncate'
})
export class TruncatePipe {
  private static DEFAULT_LENGTH = 10;
  private static DEFAULT_TRAIL = '...';

  transform(value: string, len?: number, trail?: string) : string {
    if (!len) {
      len = TruncatePipe.DEFAULT_LENGTH;
    }

    if (!trail) {
      trail = TruncatePipe.DEFAULT_TRAIL;
    }

    return value.length > len ? value.substring(0, len) + trail : value;
  }
}
```

Next let's use our pipe

```javascript
// app.ts
import {Component} from 'angular2/core';
import {TruncatePipe} from './truncate';

@Component({
  selector: 'app',
  pipes: [TruncatePipe]
  template: `
    <p>{{ 'this is a not so long string' | truncate }}</p>
    <p>{{ 'this is a not so long string' | truncate : 20 }}</p>
    <p>{{ 'this is a not so long string' | truncate : 20 : '.' }}</p>
  `
})
export class App {
  constructor() {}
}
```

## Plunker

http://plnkr.co/edit/Y3in02HYKneMNeotN6Nv?p=preview

## Learn more

For more details on Angular2 builtin pipes and creating custom pipes, check then official guide https://angular.io/docs/ts/latest/guide/pipes.html

# How to create a component with inputs and outputs ?

In this cookbook, we're going to see how to create an Angular2 component with inputs and outputs.
Inputs in angular2 components are defined as properties and outputs as events EventEmitters (Observables).
Our example will be a search form with an initial value as input and the results as output.

## Prerequisites

 * [Bootstrap an Angular2 application](bootstrap.md)
 * [Create an Angular2 service](service.md)

## Angular version

- 2.0.0-alpha.45

## How To ?

First, let's a create a simple service to search in a list of items.

````javascript
// language-repository.ts
import {Injectable} from 'angular2/angular2';

@Injectable()
export class LanguageRepository {
  items: Array<string>;
  constructor() {
    this.items = ['php', 'javascript', 'ruby', 'python', 'go', 'java'];
  }
  search(str: String) {
    return this.items.filter(item => item.indexOf(str) > -1);
  }
}
````

Define the binding for our service

````javascript
// bootstrap.ts compiled (using typescript) to bootstrap.js
import {bootstrap} from 'angular2/angular2';
import {App} from './app';

import {LanguageRepository} from './language-repository';

bootstrap(App, [
  LanguageRepository
]);
````

Let's create now our search form component

````javascript
// search-form.ts
import {Component, View, EventEmitter} from 'angular2/angular2';
import {LanguageRepository} from './language-repository';

@Component({
  selector: 'search-form',
  inputs: ['initial:initial'],
  outputs: ['success']
})
@View({
  template: `
    <form (submit)="$event.preventDefault(); search(query.value);">
      <input #query [value]="initial" />
      <button type="submit">Search</button>
    </form>
  `
})
export class SearchForm {
  success: EventEmitter;
  constructor(public languageRepository: LanguageRepository) {
    this.success = new EventEmitter();
  }
  search(query: string) {
    let results = this.languageRepository.search(query);
    this.success.next({ results: results });
  }
}
````

We can now use our search form in another component

````javascript
// app.ts
import {Component, View} from 'angular2/angular2';
import {SearchForm} from './search-form';

@Component({
  selector: 'app'
})
@View({
  directives: [ SearchForm ],
  template: `
    Results Count : {{ count }}
    <search-form [initial]="'php'" (success)="onSuccess($event.results)"></search-form>
  `
})
export class App {
  count: number = 0;
  onSuccess(results: Array<string>) {
    console.log(results);
    this.count = results.length;
  }
}
````

And that's it, easy right !!!

## Plunker

http://plnkr.co/edit/hLgpz1u8Z0GtwSeOorr6?p=preview

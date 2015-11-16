# How to create a component with inputs and outputs ?

One of the important part of any application is authentication and security.
In AngularJS we, used to have a custom attribute in each route to tell if this route is protected or not.
and intercept all route change events to check if we are allowed to access this route or no.
In this cookbook we're going to see how to implement a similar behaviour in angular 2.

* The backend login API will be simulated
* We will use localStorage to store our authentication token
* We will also how we can pass this token to http requests

## Prerequisites

 * [Bootstrap an Angular2 application](bootstrap.md)
 * [Create an Angular2 service](service.md)
 * [create a component with inputs and outputs](component-input-output.md)

## Angular version

- 2.0.0-alpha.46

## How To ?

First, let's a create a simple authentication service. It will have two roles :
 * Storing the current authentication token (if loggedin)
 * Have a logout and login methods

```javascript
// authentication.ts
import { Injectable } from 'angular2/angular2';
import { Observable } from '@reactivex/rxjs/dist/cjs/Rx';

@Injectable()
export class Session {
  token: string;

  constructor() {
    this.token = localStorage.getItem('token');
  }

  login(username: String, password: String) {
    /*
     * If we had a login api, we would have done something like this

    return this.http.post('/auth/login', JSON.stringify({
        username: username,
        password: password
      }), {
        headers: new Headers({
          'Content-Type': 'application/json'
        })
      })
      .map((res : any) => {
        let data = res.json();
        this.token = data.token;
        localStorage.setItem('token', this.token);
      });

      for the purpose of this cookbook, we will juste simulate that
    */

    if (username === 'test' && password === 'test') {
      this.token = data.token;
      localStorage.setItem('token', this.token);
      return Observable.just('token');
    }

    return Observable.throw('authentication failure');
  }

  logout() {
    /*
     * If we had a login api, we would have done something like this

    return this.http.get(this.config.serverUrl + '/auth/logout', {
      headers: new Headers({
        'x-security-token': this.token
      })
    })
    .map((res : any) => {
      this.token = undefined;
      localStorage.removeItem('token');
    });
     */

    this.token = undefined;
    localStorage.removeItem('token');

    return Observable.just(true);
  }

  isLoggedin() {
    return this.token !== undefined;
  }
}
```

Create a login component for the login route

## Plunker


# How to implement stateless authentication ?

One of the important part of any application is authentication and security.
In AngularJS we, used to have a custom attribute in each route to tell if this route is protected or not.
and intercept all route change events to check if we are allowed to access this route or no.
In this cookbook we're going to see how to implement a similar behaviour in angular 2.

* The backend login API will be simulated
* We will use localStorage to store our authentication token
* We will also see how we can pass this token to http requests

## Prerequisites

 * [Bootstrap an Angular2 application](bootstrap.md)
 * [Create an Angular2 service](service.md)
 * [create a component with inputs and outputs](component-input-output.md)

## Angular version

- 2.0.0-alpha.53

## How To ?

First, let's a create a simple authentication service. It will have two roles :
 * Storing the current authentication token (if loggedin)
 * Have a logout and login methods

```javascript
// authentication.ts
import {Injectable} from 'angular2/core';
import {Observable} from 'rxjs/Observable';

@Injectable()
export class Authentication {
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
      this.token = 'token';
      localStorage.setItem('token', this.token);
      return Observable.of('token');
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

    return Observable.of(true);
  }
}
```

Next, we create a simple utility function (used later) to check if we are loggedin or not.

```javascript
// is-loggedin.ts
export function isLoggedin() {
  return !!localStorage.getItem('token');
}
```

Create a login component for the login route

```javascript
// login.ts
import {Component} from 'angular2/core';
import {FORM_DIRECTIVES, FormBuilder, Validators, ControlGroup, NgIf} from 'angular2/common';
import {Router} from 'angular2/router';
import {Authentication} from './authentication';

@Component({
  selector: 'login',
  directives: [ FORM_DIRECTIVES, NgIf ],
  template: `
    <form [ngFormModel]="form" (submit)="onSubmit(form.value)">
      <div *ngIf="error">Check your password</div>
      <div>
        <label for="username">Username</label>
        <input type="text" ngControl="username">
      </div>
      <div>
        <label for="password">Password</label>
        <input type="password" ngControl="password">
      </div>
      <div class="form-group">
        <button type="submit" [disabled]="!form.valid">Login</button>
      </div>
    </form>
  `
})

export class Login {
  form: ControlGroup;
  error: boolean = false;
  constructor(fb: FormBuilder, public auth: Authentication, public router: Router) {
    this.form = fb.group({
      username:  ['', Validators.required],
      password:  ['', Validators.required]
    });
  }

  onSubmit(value: any) {
    this.auth.login(value.username, value.password)
      .subscribe(
        (token: any) => this.router.navigate(['../Home']),
        () => { this.error = true; }
      );
  }
}
```

This component is a basic login form that redirects to the `Home` route on success.
Now, let's create the `Home` component which needs to be secured.

```javascript
// home.ts
import {Component} from 'angular2/core';
import {Router, CanActivate} from 'angular2/router';
import {Authentication} from './authentication';
import {isLoggedin}  from './is-loggedin';

@Component({
  selector: 'home',
  directives: [],
  template: `
    <h2>I am logged in</h2>
    <a href="#" (click)="onLogout()">Logout</a>
  `
})

@CanActivate(() => isLoggedin())
export class Home {
  constructor(public auth: Authentication, public router: Router) {}

  onLogout() {
    this.auth.logout()
      .subscribe(
        () => this.router.navigate(['../Login']),
      );
  }
}
```

Notice the use of the `CanActivate` router lifecycle callback. Is allow us to restrict the component use to the logged in users only.
Last thing we need to do, is define our root component and the providers in the bootstrap script

```javascript
// app.ts
import {Component} from 'angular2/core';
import {RouteConfig, ROUTER_DIRECTIVES} from 'angular2/router';
import {Home} from './home';
import {Login} from './login';

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
    { path: '/login', as: 'Login', component: Login }
])
export class App {
  constructor() {}
}
```

```javascript
// bootstrap.ts
import {provide} from 'angular2/core';
import {bootstrap} from 'angular2/platform/browser';
import {App} from './app';
import {ROUTER_PROVIDERS, LocationStrategy, HashLocationStrategy} from 'angular2/router';
import {Authentication} from './authentication';

bootstrap(App, [
  ROUTER_PROVIDERS,
  provide(LocationStrategy, {useClass: HashLocationStrategy}),
  Authentication
]);
```

## Plunker

http://plnkr.co/edit/j69yu9cSIQRL2GJZFCd1?p=preview (user 'test', password 'test)

## Learn more

You want to use JWT tokens, may be you can take a look at this library [https://github.com/auth0/angular2-jwt](https://github.com/auth0/angular2-jwt)

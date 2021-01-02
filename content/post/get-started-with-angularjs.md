---
title: Get started with AngularJS
toc: true
id: 1246
categories:
  - Javascript
date: "2015-05-18T23:32:13+00:00"
---

### AngularJS concepts

![angularjs-conception](/media/angularjs-conception.png)

Core concepts:

*   2-way data binding
*   directive
*   expression &amp; filter
*   view / template
*   controller

#### 2-way data binding

![jogjajs-single-page-application-nganggo-angularjs-17-638](/media/jogjajs-single-page-application-nganggo-angularjs-17-638.jpg)

#### directives

*   manipulate DOM: DOM manipulation should only happen in directive implementations
*   manipulate data
Elements: &lt;<span style="color: #000000; background-color: #ffff00;">ng-xx</span>&gt; &lt;/ng-xx&gt;

Attributes: &lt;span <span style="background-color: #ffff00;">ng-xx="exp"</span>&gt;&lt;/span&gt;

Comments: &lt;!-- directive: <span style="background-color: #ffff00;">ng-xx exp</span> --&gt;

Classes: &lt;span class="<span style="background-color: #ffff00;">ng-xx: exp</span>"&gt;&lt;/span&gt;

#### Scope

Scope is a glue between controller, template and model. It keeps models and views separate, but in sync.

#### Controller

Controller is for UI logic

### Injector

The injector uses recipes to create two types of objects, **services** and **specialized objects**.

*   Services are objects whose API is defined by the developer writing the service.
*   Specialized objects conform to a specific Angular framework API. These objects extend the framework as plugins and therefore must implement interfaces specified by Angular. These interfaces are Controller, Directive, Filter and Animation.

#### Recipes

There are five recipe types that define how to create objects: Value, Constant, Factory, Service and Provider.

Value, Constant, Factory and Service are the synaptic sugar of Provider.

*   Value recipe


```javascript
myApp.value('clientId', 'a12345654321x');
```

&nbsp;

*   Constant recipe


```javascript
myApp.constant('planetName', 'Greasy Giant');
```


*   Factory recipe


```javascript
myApp.factory('clientId', function clientIdFactory() {
  return 'a12345654321x';
});
```


*   Service recipe


```javascript
function UnicornLauncher(apiToken) {

  this.launchedCount = 0;
  this.launch = function() {
    // Make a request to the remote API and include the apiToken
    ...
    this.launchedCount++;
  }
}

//myApp.factory('unicornLauncher', ["apiToken", function(apiToken) {
//  return new UnicornLauncher(apiToken);
//}]);

myApp.service('unicornLauncher', ["apiToken", UnicornLauncher]);
```


*   provider recipe


```javascript
myApp.provider('unicornLauncher', function UnicornLauncherProvider() {
  var useTinfoilShielding = false;

  this.useTinfoilShielding = function(value) {
    useTinfoilShielding = !!value;
  };

  this.$get = ["apiToken", function unicornLauncherFactory(apiToken) {

    // let's assume that the UnicornLauncher constructor was also changed to
    // accept and use the useTinfoilShielding argument
    return new UnicornLauncher(apiToken, useTinfoilShielding);
  }];
});

myApp.config(["unicornLauncherProvider", function(unicornLauncherProvider) {
  unicornLauncherProvider.useTinfoilShielding(true);
}]);
```

<table class="table table-bordered code-table ng-scope" border="1">
<thead>
<tr>
<th>Features / Recipe type</th>
<th>Factory</th>
<th>Service</th>
<th>Value</th>
<th>Constant</th>
<th>Provider</th>
</tr>
</thead>
<tbody>
<tr>
<td>can have dependencies</td>
<td class="success">yes</td>
<td class="success">yes</td>
<td class="error">no</td>
<td class="error">no</td>
<td class="success">yes</td>
</tr>
<tr>
<td>uses type friendly injection</td>
<td class="error">no</td>
<td class="success">yes</td>
<td class="success">yes*</td>
<td class="success">yes*</td>
<td class="error">no</td>
</tr>
<tr>
<td>object available in config phase</td>
<td class="error">no</td>
<td class="error">no</td>
<td class="error"><span style="background-color: #99cc00;">no</span></td>
<td class="success"><span style="background-color: #99cc00;">yes</span></td>
<td class="success">yes**</td>
</tr>
<tr>
<td>can create functions</td>
<td class="success">yes</td>
<td class="success">yes</td>
<td class="success">yes</td>
<td class="success">yes</td>
<td class="success">yes</td>
</tr>
<tr>
<td>can create primitives</td>
<td class="success">yes</td>
<td class="error">no</td>
<td class="success">yes</td>
<td class="success">yes</td>
<td class="success">yes</td>
</tr>
</tbody>
</table>

#### Specialized object

The instructions for the injector to create these special objects (with the exception of the Controller objects) use the Factory recipe behind the scenes.

Since the directives are registered via the Factory recipe, we can use the same syntax as with factories.



```javascript
myApp.directive('myPlanet', ['planetName', function myPlanetDirectiveFactory(planetName) {
  // directive definition object
  return {
    restrict: 'E',
    scope: {},
    link: function($scope, $element) { $element.text('Planet: ' + planetName); }
  }
}]);
```

Using Factory recipes, you can also define Angular's filters and animations, but the controllers are a bit special.


```javascript
myApp.controller('DemoController', ['clientId', function DemoController(clientId) {
  this.clientId = clientId;
}]);
```

Unlike services, controllers are not singletons.

&nbsp;

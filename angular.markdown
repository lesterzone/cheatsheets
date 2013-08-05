## Angular

### Directives

*	ng-repeat
~~~html
<li ng-repeat="variable in someArray">{{variable}}</li>
~~~

*	ng-model
~~~html
<input ng-model="foobar"/>
~~~

*	ng-repeat *with filters*
~~~html
<li ng-repeat="variable in someArray | orderBy:variableProperty">{{variablePropery}}</li>
<li ng-repeat="variable in someArray | orderBy:variableProperty">{{variableProperty | uppercase}}</li>
~~~

### Data Binding
~~~html
{{someVariable}}
~~~

### Controller:
~~~javascript
function simpleController($scope){
	return "This is ";
}
~~~

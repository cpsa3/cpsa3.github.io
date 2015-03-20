---
layout: post
title: "Communication in AngularJS with the Pub/Sub Design Pattern"
date: 2015-03-20 22:32:29 +0800
comments: true
categories: ['前端']
---
###Code
```javascript
var MyApp = angular.module('MyApp');
 
MyApp.service('messageBus', ['$rootScope', function($rootScope) {
    var messageBus = {};
 
    messageBus.publish = function(msg, data) {
        data = data || {};
        $rootScope.$emit(msg, data);
    };
 
    messageBus.subscribe = function(msg, scope, func) {
        var unbind = $rootScope.$on(msg, func);
        if (scope) {
            //remove the listener when $scope is destroyed
            scope.$on('$destroy', unbind);
        }
        //return the unsubscribe function so the user can do their own memory management
        return unbind;
    };
 
    return messageBus;
}]);
```

###Example of use
Subcribe
```javascript
//you can call unsubcribe to clear the reference to this service and prevent memory leaks
var unsubcribe = messageBus.subscribe('index.load', $scope, function(event, data) {
    $scope.navModel = data;
});
```

Publish
```javascript
messageBus.publish('index.load', data);
```


###References
* http://toddmotto.com/all-about-angulars-emit-broadcast-on-publish-subscribing/
* https://github.com/angular/angular.js/issues/4574
* http://stackoverflow.com/questions/11252780/whats-the-correct-way-to-communicate-between-controllers-in-angularjs
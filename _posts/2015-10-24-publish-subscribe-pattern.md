---
layout: post
title: Publish-subscribe Pattern
---

While building _Hack Reactor: The Game_, we often run into the situation where Angular controllers need to communicate with each other. For example, when a game starts, it generates a set of puzzles and the manual required to solve them.

![Hack Reactor Game Manual]({{ site.baseurl}}/images/hackManual.png "Authentic Hacking Experience")

Since the game screen and the manual are located in different Angular views, we need a way for the game controller to tell the manual controller that the manual is ready to be loaded. A good way to accomplish this is using the **Publish-subscribe (Pub/Sub) pattern**.

## Implementation
To keep things organized, we've put all the Pub/Sub functions in an Angular service factory:

```javascript
angular.module('app.comm', [])

.factory('requestNotification', ['$rootScope',
  function($rootScope) {
    // private notification messages
    var _LOAD_MANUAL_ = '_LOAD_MANUAL_';

    // publish load manual notification
    var loadManual = function(manual) {
      $rootScope.$broadcast(_LOAD_MANUAL_, manual);
    };

    // subscribe to load manual notification
    var onLoadManual = function($scope, handler) {
      $scope.$on(_LOAD_MANUAL_, function(event, manual) {
        handler(manual);
      });
    };

    return {
      loadManual: loadManual,
      onLoadManual: onLoadManual
    };
  }
]);
```

 When our game controller generates the manual, it will call the **loadManual** function, which **publishes**/broadcasts the \_LOAD\_MANUAL\_ event along with the manual content in $rootScope for any controller that is actively listening for the event:

```javascript
angular.module('app.game', [])

.controller('GameController', [$scope', 'requestNotification',
  'Puzzle',
  function($scope, requestNotification, Puzzle) {
    // Puzzle service generates a set of puzzles
    // with the associated manual
    var puzzles = Puzzle.generatePuzzles();

    // publish the loadManual event
    requestNotification.loadManual(puzzles.manual);
  }
]);
```

Any controller that needs to listen for the event has to **subscribe** to it by calling the **onLoadManual** function. When the event is triggered, the specified code in the Pub/Sub service will run. In this case, the **onLoadManualhandler** callback function will be called with the manual content passed in:

```javascript
angular.module('app.manual', [])

.controller('ManualController', [$scope', 'requestNotification',
  function($scope, requestNotification) {
    var onLoadManualHandler = function(manual) {
      // store the manual in the scope of this controller
      $scope.manual = manual;
    };

    // subscribe to loadManual event
    requestNotification.onLoadManual($scope, onLoadManualHandler);
  }
]);
```

With the manual content loaded in _ManualController_, the manual content can now be properly displayed in the Angular view.
Stop the Clock
==============

A Nim like game designed to give young children practice in handling time while in a game context.

Step 3 
------

Continuing, checkout Step3 to see the next phase.

```
cd StopTheClock
git pull 
git checkout Step3
```

I worked on the unit tests first in `test/spec/controllers/main.js`, expanding them until they captured all the required game play. These are reasonably well commented so you should find your way around them using http://pivotal.github.io/jasmine/ as a reference.

This gave lots of failures in `grunt test` as expected.

Notice how you have to pass parameters to the controller in the test environment through an object. The test environment has to make these parameters first. We have the option of faking them (that happens with routeParams which would normally come from the URL), or of injecting them ( _$route_ and _$timeout_ are examples), or a combination of both ($scope). 

If you need to refer to these parameters from a test, then they have to be stored in the more global context of the `describe` call so the tests can find them. This happens with $scope, $route and $timeout.

The only tricky test is the last one which is looking at behaviour happening on a 1 second timeout. It's important to understand that when running `grunt test`, you are running a test application configured by `karma.conf.js`, and that pulls in some replacements for awkward to test code like timeouts.

So the $timeout that we are using in `grunt test` doesn't actually time anything. Instead the `$timeout fn, 1000` call in the controller will queue the function `fn` to be executed in 1000ms (notionally). In the test spec we call `$timeout.flush 1000` - which triggers that function by pretending that 1000ms has elapsed (immediately). That allows us to test the expected results immediately instead of having to write a test inside some asynchronous callback.

There's some subtlety with this $timeout test that gets resolved in Step5. For the moment we make it pass by setting the invokeApply parameter to false in the controller call to $timeout. It turns out that this really should be 'true', and in Step5 we adjust the test to cope with the strange error (an unexpected GET request for 'views/main.html' to $httpBackend) that arises.

To understand what's going on here, it's necessary to read up on the [Angular execution context](http://docs.angularjs.org/guide/scope), the $scope.$apply function and the digest loop.

These tests were reasonably easy to write ahead of the implementation, and served as a good way to formalise the specification. The test/spec folder _is_ the specification in some sense. Unit tests like these can test how functions should work. 

I then added these functions into the controller and worked on them until all the tests passed. 

And then I added in a small bug for your amusement, causing 3 tests to fail again.

We've been testing the controller here. The controller maintains the data model of the application. It knows absolutely nothing about the view. In Step 4, we'll go back to the view and add in references to the new variables and functions we've had to add to the model to get the game play we wanted. That should finish the job.

As well as controllers, the angular documentation talks a lot about directives, services, and filters. In this simple application we only need to create a custom controller, and a single view. We will use a lot of directives provided by angular by adding them to the HTML in the view, and we will also use one filter. This turns out to be a common pattern for in-page interactivities.


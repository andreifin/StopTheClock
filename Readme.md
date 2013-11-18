Stop the Clock
==============

A Nim like game designed to give young children practice in handling time while in a game context.

Step 2
------

In Step 1 we made a clock that works in the summer. We didn't give it enough intelligence to detect whether Summer Time (Daylight Savings Time) is  current, so in winter it runs an hour fast. That's OK - we're going to delete that part of the code and replace it with the gameplay code.

This time the big topic is testing.

The idea now is to implement the gameplay in the main controller as far as we can using 'test driven development'. 

To set this up pull the latest release, and checkout Step2
$ cd StopTheClock
$ git pull
$ git checkout Step2

Test Driven Development works like this:

1. We pick a feature to implement next.
2. We first write a test for some small part of the new feature, and run it.
3. The test should fail first time - because it's not implemented.
4. We then write the smallest amount of code necessary to make the test pass.
5. We repeat steps 2,3,4 until we've tested the required behaviour of the new feature

We now have the full required behaviour implemented AND a set of automated tests that we can run again in the future when something changes (like a new browser version appearing). 

The more we can cover the code with tests like this, the easier it is to maintain the code later - it's the single most important difference between coding in Flash and coding for HTML5. In Flash, we could rely on players being backwards compatible. With javascript running in browsers we can't. 

In the Step 2 code, I've written 2 tests - one that checks the minute hand turns the correct number of degrees for 30 minutes, and one that checks
the hour hand moves the correct number of degrees to display 10:30.

To run the tests say `grunt test`.

This will start up a new browser window in which to run the tests - it will look like this - you can reduce its size, but keep it visible or the tests will run much slower than they should.

No tests will run until one of your source files changes, so let's do that.

Edit app/scripts/controller/main.ls and change line 18 to:
```
  | 'hour' => turn = "rotate(#{30 * $scope.hours + $scope.minutes/3}deg)"
``` 
It will now be calculating an incorrect turn for the hour hand. Save the file, to trigger the test. In the terminal you should see:

```
Running "karma:unit:run" (karma) task
[2013-11-10 23:29:15.280] [DEBUG] config - Loading config /Users/gmp26/angular/StopTheClock/karma.conf.js
Chrome 30.0.1599 (Mac OS X 10.7.5) Controller: MainCtrl should turn hour hand by 315deg for 10 hours and 30minutes FAILED
  Expected 'rotate(310deg)' to equal 'rotate(315deg)'.
  Error: Expected 'rotate(310deg)' to equal 'rotate(315deg)'.
      at null.<anonymous> (/Users/gmp26/angular/StopTheClock/.tmp/spec/controllers/main.js:19:60)
Chrome 30.0.1599 (Mac OS X 10.7.5): Executed 2 of 2 (1 FAILED) (0.098 secs / 0.03 secs)
```

It's saying that the minute hand turned the right amount, but the hour hand turned 300 degrees instead of the expected 315 degrees.

```
Change line 18 back to the correct version and save again. You should then see in the terminal:
Running "karma:unit:run" (karma) task
[2013-11-10 23:41:11.042] [DEBUG] config - Loading config /Users/gmp26/angular/StopTheClock/karma.conf.js
Chrome 30.0.1599 (Mac OS X 10.7.5): Executed 2 of 2 SUCCESS (0.197 secs / 0.027 secs)
```

Now take a look at the test code in `test/spec/controllers/main.ls'. This is following the pattern used by the jasmine test framework - which has excellent documentation at http://pivotal.github.io/jasmine/ which you'll need to read through. 

I've added lines 14 and 15 to the standard setup code to set the clock variables up to 10 hours and 30 minutes, and then added the two tests which check the operation of the turn function. 
```
  it 'should turn minutes hand by 180deg for 30 minutes', ->
    expect(scope.turn('minute')["-webkit-transform"]).toEqual "rotate(180deg)"

  it 'should turn hour hand by 315deg for 10 hours and 30minutes', ->
    expect(scope.turn('hour')["-webkit-transform"]).toEqual "rotate(315deg)"
```
Tests like this are usually short and sweet - you create them to test a function, and you check that the function is giving the expected result for some given inputs. Sometimes you need to provide a few different inputs and expected outputs to make sure that you've checked the function works properly. 

These 2 tests are probably sufficient here, but as an exercise, add another test to check that the turn function returns an undefined value (on key '-webkit-transform') if asked for the seconds hand turn. Checkout Step3 for more tests and a solution to this.

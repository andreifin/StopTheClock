Stop the Clock
==============

A Nim like game designed to give young children practice in handling time while in a game context.

Step 1
-----

In Step 0 we went through the commands necessary to scaffold a project from scratch - giving us the standard Yeoman 'Allo 'Allo screen. 

This time we're going to make a working Summertime-only clock inside that scaffold.

Yeoman generators tend to be updated quite frequently as demands change, so you may find it easier to reach the starting point for this work by checking out Step0

```
git clone https://github.com/gmp26/StopTheClock.git Step0

```

That will create a Step0 directory, but it won't have installed the node modules and bower modules it needs. To do that:
```
cd Step0 
npm install
bower install
```
To test everything is working,
grunt server
(Ctrl-C to stop the server)

Secondly, lets go straight to the next version which has a working clock. This is also in my github account, but in a branch called 'Step1'. The process is similar, but we'll create a Step1 directory and checkout the Step1 branch into it after cloning…

```
cd ..
git clone https://github.com/gmp26/StopTheClock.git Step1
cd Step1
git checkout Step1    # checkout the Step1 branch
npm install
bower install
```

To test everything is working,
grunt server

You should see this at localhost:9000, and the clock should be running.

So what's changed between Step0 and Step1? Actually, not very much :)

* There are some new clock graphics in app/img
* There are some new styles in  app/styles/main.less
* There's some some new HTML in app/views/main.html, which assembles the clock graphics into a clock using those styles.
* And there's some new code in app/scripts/controllers/main.ls which updates the clock every second.

The time in text is really simple - it comes from the HTML <h2>The time is {{hours}}:{{minutes}}</h2> line, 
where {{hours}} and {{minutes}} are interpolating values defined as $scope.hours and $scope.minutes by the controller at app/scripts/controllers/main.ls.

The analogue clock is doing much the same thing, except that it is calling a $scope.turn function to retrieve the CSS style commands necessary to turn the hands. These are a little verbose as each browser needs a different prefix at the moment.

I looked up the necessary CSS commands by googling 'MDN CSS transform rotate' to get to [this page on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/transform‎). The Mozilla Developer Network is a good source for all HTML, CSS and javascript docs. It's usually more accurate than the W3Cschools site that tends to rank high in google. 
 
To keep things simple, I've made `png` files for the clock background, the hands, and their shadows all as `400px x 400px`files with transparency. The centre axis of the clock is at exactly `(200px, 200px)` so we can change the time by a simple rotation about the centre. These are layered on top of each other in main.html, and they're given CSS classes to ensure that they are positioned absolutely on top of each other. The shadow images are offset a bit to give a drop shadow effect to the hands.

There is a little complexity in the HTML and CSS to do with ensuring that the clock scales properly so it will look good on a mobile - try making the browser window really small and you should see that the clock scales to cope. The way we do this is by ensuring all the components have width and height set to 100% - so they scale to the size of their parent. 

The topmost `.vis` component is given the width and height of the browser page. The width is OK, but the height will be wrong unless it happens to match the width. 

So we nest a `.set-height div` with the same width at `100%`, but we set its height to be the same as its width by setting its `top-padding` at 100% (of the width) - that's a trick I found on [StackOverflow](http://stackoverflow.com/questions/1311068/scale-a-div-to-fit-in-window-but-preserve-aspect-ratio). The clock nests in this space which is now guaranteed to be square, whatever the screen size.

Finally, the HTML adds a style of `ng-style="turn('minute')"` or `ng-style="turn('hour')"` to the clock hands and hand shadows. This is where AngularJS comes in. When the page is loaded, it reads the HTML, and notices those ng-style directives, and compiles them. It treats turn('minute') and turn('hour') as expressions to be evaluated on a 'scope' variable. The scope is a javascript object defined by the angular controller 'MainCtrl'.

The scope is shared by the HTML and the controller. Take a look at the controller, and you'll see that it defines `$scope.turn` as a function which returns a CSS rotate transform corresponding to the angle through which the hands must be turned.

Notice that `app/scripts/app.ls` sets up a table that routes the main root url of the app `'/'` to the `main.html` view and attaches the `MainCtrl` controller. 

Notice how neat this is. HTML does what HTML does best - it lays out the page. Angular gives us some extra HTML directives that can read a scope object and update the HTML dynamically. 

CSS worries about style as it should.

The script only needs to worry about updating the scope object - it supplies $scope.hours, $scope.minutes and the function $scope.turn.  The controller scripts never manipulates the HTML directly - we always leave that to the angular directives (ng-style and the {{}} interpolator directives here).

OK - I hope you can begin to see how we can achieve quite a lot with not very much code this way. Go back and study the Step0 code - you should see that it follows exactly the same conventions as does Step1.

After that I'd recommend getting into angular a little more using the excellent series of videos at http://egghead.io.
Videos 1,2 and 5 cover the topics we've used so far.

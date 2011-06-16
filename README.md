Viper is a fairly low-level animation library that allows you to change a property of an object over time.

Using Viper is pretty easy. Call the Viper constructor with a set of options, like this:

    var obj = {x: 0}, animation = Viper({ // new is optional
        object: obj,
        property: 'x',
        to: 100
    });

or to animate a CSS property:

    var elem = document.getElementById('myElem'), animation = Viper({
        object: elem.style,
        property: 'backgroundColor', // note this has to be in camelCase format
        from: '#ffffff', // rgb or hex, no shorthand hex (#fff) or color names (red)
        to: '#000000'
    });

You can then start it by calling the `start` method. If you want to cancel the animation, call the `stop` method. To pause, call `pause` (boy, I'm clever with these names), and to resume, call... you know.
`pause` takes an optional number argument, which is the number of milliseconds in which the animation will automagically be started again.

The set of options that the Viper constructor takes is listed here (option (type) - default - description):

- object (Object) - null - The object whose property should be animated.
- property (String) - null - The property to animate.
- from (String | Number) - whatever the property is currently set to - The start value for the animation.
- to (String | Number | Array[String | Number]) - null - The end value for the animation. If this is an array, it will animate to the first value, set `from` to that, animate to the next value, etc, until it runs out of values.
- transition (Function) - Viper.Transitions.linear - The transition function to use. Can be `Viper.Transitions.linear`, `Viper.Transitions.sine`, `Viper.Transitions.bounce`, or `Viper.Transitions.elastic`. `.out` and `.inOut` are available on all of those.
- duration (Number) - 500 - The number of milliseconds the animation should last.
- fps (Number) - 40 - The number of frames to process each second. Generally shouldn't be lower than 25 or higher than 60.
- start (Function | Array[Function]) - empty array - A function or functions to run when the `start` method of this object is called. The functions are called after the animation has been started, so you can cancel it if you want. The functions are bound to the current animation and are passed the object whose propery is being animated.
- update (Function | Array[Function]) - empty array - Same as above, except the functions are called after each frame is processed.
- finish (Function | Array[Function]) - empty array - Same as above, except the functions are called after the animation is stopped.

And here are all the methods:

- start() - Starts the animation then fires all `start` event handlers.
- stop() - Stops the animation dead tracks then fires all `stop` event handlers. If `start` is called again the animation will start from the point it was stopped at, making this not much different from `pause`, but this behavior is not guaranteed. Use pause if you really mean it.
- pause([time]) - Pauses the animation. When the animation is resumed it will start from where it left off. If `time` is defined, it will automatically restart the animation after `time` milliseconds. Does not fire any events.
- resume() - Resumes the animation from where it left off. Does not fire any events.

You can have a permanently looping animation by using the `to` property and the `finish` callback:

    Viper({
        object: myDiv.style,
        property: 'letterSpacing',
        from: '0px',
        to: ['10px', '0px'],
        transition: Viper.Transitions.bounce.out,
        duration: 600,
        finish: function () { this.start(); }
    }).start();

Note about the `from` and `to` options:
These can be either a color in rgb or hex format or a number or a string containing a number. The end value of the property being animated will *always* be a string, though it is not guaranteed things will stay that way.
RGB colors can be specified any way you like, as long as they have three numbers separated by commas. You can do '0,0,0', 'rgb(255, 255, 255)', or if you want to be confusing, 'hsl(7, 141, 214)'. It is possible Viper will support hsl colors in the future though...
Numbers can be wrapped between whatever strings you like, so for IE you can animate 'alpha(opacity=100)' to 'alpha(opacity=0)' and it'll work as expected. The parser's pretty flexible; the only thing it can't do is animate part of a string, so if you have `transform: rotate(30deg) scale(1.1)` you'll only be able to animate the `rotate` part. That will probably change in the future with a `parseAt` option, but for now... sorry.

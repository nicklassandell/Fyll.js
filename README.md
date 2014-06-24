# Fyll.js

Fyll is a lightweight and extendable library for filling out forms in a human manner. The primary use case is to demo forms, though feel free to hack it up or use it in any way you want. Fyll does not depend on any other libraries.

**Fyll.js is still being actively developed and is not considered stable at this time.**

## How do i use it?

Fyll uses an expressive syntax for setting instructions. Just include the library and run the `go` method with your instructions. Here's an example:

    fyll.go('fill input1 then fill input2 then click submit');
    
Each instruction is separated by `then`. An instruction consists of two parts, an action and a value. In the above case, the first instruction has the action `fill` and the value `input1`. The `fill` and `click` actions will assume the value is an element ID. The `fill` action will look for a `data-fyll` attribute on the target element to fill the input with.

You can pass options to to Fyll as a second parameter. Here are the available options with their default values:

    fyll.go('your instruction', {
        actionDelay: 500,           // Delay between actions
        keyPressInterval: 100,      // Time for a keypress
        humanisePressInterval: true,// Enabling this will randomize the keyPressInterval
        focusClass: 'fyll-focus',   // Class to be added to focused elements (fill and click actions makes use of this by default)
        complete: false             // Callback to run when all instructions has completed. If you do not wish to set any other options, you can pass the callback function as a second parameter to fyll.go().
    });

## Actions

### fill
Expects the value to be an element ID. The fill value should be supplied through a `data-fyll` attribute on the target input. The `fill` action works with textareas and inputs.

### click
Expects the value to be an element ID. 

### toggle
Expects the value to be an element ID. This action will toggle the `checked` property of the target element.

### pause
Value must be a time duration specified in miliseconds. This action will pause for the supplied amount of time before continuing with the next action.

## Custom actions
Does your action not exist? No worries, Fyll let's you add your own actions to suit your needs.

To add a custom action you simply append it to the global `fyll.actions` object. Your action should consist of an object with the following values:

    fyll.actions.yourActionName = {
    
        // If set to true, a delay (see actionDelay in config) will be added between your action
        // and the next one. This should normally be set to true.
        pauseAfter: true,
        
        // Your action code
        execute: function(value, callback) {
            
        }
    };

The `execute` function will accept two values. The first one is the value supplied to the action, and the second one is the callback which has to be run once the action is complete. Not running the callback will prevent Fyll from continuing with the next instructions in the queue. Do not forget to run the callback.

Let's create a custom action that blinks an object for a second:

    // Append your action to the global `fyll.actions` object.
	fyll.actions.blink = {
		pauseAfter: true,
		execute: function(value, callback) {
			var target = document.getElementById(value);

			// Let's be considerate and do some error checking.
			// The target element was not found
			if(!target) {

				// Throw error in console
				console.warn('Fyll: The target element #'+ value +' was not found.');

				// Run callback
				callback();

			// Element was found, all's good!
			} else {

				// Hide target element
				target.style.opacity = 0;

				// Wait a second
				setTimeout(function() {

					// Show target element
					target.style.opacity = 1;

					// We're done, so let's run the callback so Fyll can
					// continue with the rest of the instructions.
					callback();
				}, 1000);
			}
		}
	};
	
Your action can make use of a couple helper functions, to make your life easier. Here are the available helpers:

#### fyll.actionHelpers.setFocus(target, callback)
This helper will add a focus class (see focusClass in the config) to the supplied target element. It expects a DOM object as a first parameter, and a callback as a second. The callback will be run once the focus class has been added/removed from the target element.

#### fyll.actionHelpers.removeFocus(target, callback)
Identical to `setFocus` but removes focus instead.

## Bugs?
Did i mess something up? Don't hesitate to throw me an issue, or even better a pull request.

## License
The MIT License (MIT)

Copyright (c) 2014 Nicklas Sandell

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

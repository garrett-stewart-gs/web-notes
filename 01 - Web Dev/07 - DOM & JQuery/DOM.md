DOM - Document Object Model
* Created by the browser, from an html document.
* It is a representation of an html document that divides it into elements/tags, which are also objects. Nested elements and tags create 'layers'
* Javascript (or other programming languages) can be used to manipulate the element/tag objects, which allows web pages to become dynamic.
* Javascript is an asynchronous language, so it has the ability to listen for events (clicking, data being retrieved, etc) and handle each event accordingly.

Bubbling - When events get triggered on an html element, and then that event echos (triggers again) on the next parent, and the one after that, all the way up to the html document object. You can stop bubbling with the method called: event.stopPropagation(). However, this will allow other event handlers on the current element to run. To stop all event handlers on the same element from running, you can use the method: event.stopImmediatePropagation(). Don't stop propagation unless you need to though! It can cause unintended issues.
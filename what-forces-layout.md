# What forces layout

All of the below properties or methods, when requested/called in JavaScript, will force the browser to synchronously calculate the style and layout. This is also called reflow or layout thrashing, and is common performance bottleneck.

### Element

##### Box metrics
* `elem.offsetLeft;`, `elem.offsetTop;`, `elem.offsetWidth;`, `elem.offsetHeight;`, `elem.offsetParent;`
* `elem.clientLeft;`, `elem.clientTop;`, `elem.clientWidth;`, `elem.clientHeight;`
* `elem.getClientRects();`, `elem.getBoundingClientRect();`

##### Scroll stuff
* `elem.scrollWidth;`, `elem.scrollHeight;`
* `elem.scrollLeft;`, `elem.scrollTop; // also setting them`
* `elem.scrollIntoView();`, `elem.scrollIntoViewIfNeeded();`  
* `elem.scrollBy();`, `elem.scrollTo();`

##### Focus
* `elem.focus(); // can trigger a *double* forced layout`  ([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/dom/Element.cpp&q=updateLayoutIgnorePendingStylesheets%20-f:out%20-f:test&sq=package:chromium&l=2369&ct=rc&cd=4&dr=C))

##### Also…
* `elem.computedRole;`, `elem.computedName;`  
* `elem.innerText;` ([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/dom/Element.cpp&q=updateLayoutIgnorePendingStylesheets%20-f:out%20-f:test&sq=package:chromium&l=2626&ct=rc&cd=4&dr=C))

### Range

* `range.getClientRects();`, `range.getBoundingClientRect();`

### Mouse events

* `mouseEvt.layerX;`, `mouseEvt.layerY;`, `mouseEvt.offsetX;`, `mouseEvt.offsetY;`

[source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/events/MouseRelatedEvent.cpp&q=f:mouserelatedevent%20computeRelativePosition&sq=package:chromium&type=cs&l=132)


### getComputedStyle 

`window.getComputedStyle(); // will typically force style recalc` ([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/dom/Document.cpp&sq=package:chromium&type=cs&l=1860&q=updateLayoutTreeForNodeIfNeeded))

`window.getComputedStyle(); // will force layout, as well,` if one of the following: 

1. The element is in a shadow tree
1. There are media queries (viewport-related ones); specifically, one of the following: ([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/css/MediaQueryExp.cpp&sq=package:chromium&type=cs&l=163&q=MediaQueryExp::isViewportDependent))
  * `min-width`, `min-height`, `max-width`, `max-height`, `width`, `height`
  * `aspect-ratio`, `min-aspect-ratio`, `max-aspect-ratio`
  * `device-pixel-ratio`, `resolution`, `orientation` 
1. The property requested is one of the following:  ([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/css/CSSComputedStyleDeclaration.cpp&sq=package:chromium&l=457&dr=C&q=isLayoutDependent))
  * `height`, `width`
  * `top`, `right`, `bottom`, `left`
  * `margin` [`-top`, `-right`, `-bottom`, `-left`, or *shorthand*] only if the margin is fixed.
  * `padding` [`-top`, `-right`, `-bottom`, `-left`, or *shorthand*] only if the padding is fixed.
  * `transform`, `transform-origin`, `perspective-origin`
  * `translate`, `rotate`, `scale`
  * `webkit-filter`, `backdrop-filter`
  * `motion-path`, `motion-offset`, `motion-rotation`
  * `x`, `y`, `rx`, `ry`

### document

* `doc.scrollingElement; // only forces style`

### window

* `window.scrollX;`, `window.scrollY;`
* `window.innerHeight;`, `window.innerWidth;`

* `window.getMatchedCSSRules(); // only forces style`


### Forms

* `inputElem.select();`, `textareaElem.select();`

([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/html/HTMLTextFormControlElement.cpp&q=updateLayoutIgnorePendingStylesheets%20-f:out%20-f:test&sq=package:chromium&l=192&dr=C))

### contenteditable
  
* Lots & lots of stuff
* …including copying an image to clipboard ([source](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/editing/Editor.cpp&sq=package:chromium&l=420&dr=C&rcl=1442532378))
  

## Appendix
##### Browsing the Chromium source:
* forced layout (and style recalc): [`updateLayoutIgnorePendingStylesheets` - Chromium Code Search](https://code.google.com/p/chromium/codesearch#search/&q=updateLayoutIgnorePendingStylesheets%20-f:out%20-f:test&sq=package:chromium&type=cs)
* forced style recalc: [`updateLayoutTree` - Chromium Code Search`](https://code.google.com/p/chromium/codesearch#search/&q=updateLayoutTree%20-f:out&p=1&sq=package:chromium&type=cs)

## Easily implement Infinite Scrolling using Intersection Observer in vanilla JavaScript

## What is infinite scrolling?
It is when more items keep loading as we scroll. Below is a GIF for visual representation.

![Imgur](https://imgur.com/5aobhq8.gif)
[View this GIF in action](https://codesandbox.io/s/infinite-scrolling-5ofl9?file=/package.json)

## Difficult way to do it (using [`getBoundingClientRect`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect))
Here we are discussing the difficult way in brief. It is to build up are appreciation for the better method (i.e Intersection Observer).

To implement infinite scrolling we would have to add a `scroll` event listener to a HTML Element.  This will fire a callback every time the element gets scrolled.

In the callback we can use [getBoundingClientRect](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) to determine the relative position of any element. getBoundingClientRect returns us the `left`, `top`, `right`, `bottom` property of the element.

![](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect/element-box-diagram.png)

Using `left`, `top`, `right`, `bottom`we can determine if the element is in our view port. If it is then we can add more items to our list or do perform some other action.

Here is a demo of [`getBoundingClientRect` function.](https://bqv9r.csb.app/)

### Reasons to not use `getBoundingClientRect`

1. Complicated and requires to write more code. Hence, more chances of bugs.
2. Performance issues: The callback fires with each scroll and computes the logic. It all also runs on the main thread.

## Easy way to do it (using [`IntersectionObserver`](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API))

Using IntersectionObserver we can observe any target element for its intersection with another element or the viewport. In simpler words, if the target element enters or leaves our viewport then a callback will be fired.

Note - Old browsers may not support the IntersectionObserver API. Check the latest support at [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#browser_compatibility).

### HTML structure of our example
```html
<main  id="main">
  <h2>List of Random Names</h2>
  <ul id="ul"></ul>
  <button id="load">Load more names</button>
</main>
```
We have a unordered list (`ul`) and a `button` at the end of the list. This is how the above HTML structure looks when rendered.

![](https://i.imgur.com/26Hoqcy.png)
### Observing the 'load more names' button using IntersectionObserver

We will add an IntersectionObserver over the 'load more names' button. Whenever the button enters our view port a callback will be fired which will add `li` tags under the existing `ul` tags.

#### Adding an IntersectionObserver to the button
```js
const loadBtn = document.getElementById('load')

// Observe loadBtn
const options = {
  // Use the whole screen as scroll area
  root: null,
  // Do not grow or shrink the root area
  rootMargin: "0px",
  // Threshold of 1.0 will fire callback when 100% of element is visible
  threshold: 1.0
};

const observer = new IntersectionObserver((entries) => {
  // Callback to be fired
  // Entries is a list of elements out of our targets that reported a change.
  entries.forEach((entry) => {
    // Only add to list if element is coming into view not leaving
    if (entry.isIntersecting) {
      // Perform some operation here
    }
  });
}, options);

observer.observe(loadBtn);
```

For detailed information on the options to IntersectionObserver visit the [MDN page](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API).

#### Adding items to the list
Using the above code we have added an observer to observe the load button. Now we need a function to fetch and add items to the list.

We will call this inside the callback.

```js
async function addNamesToList(ul_id, num) {
  const ul = document.getElementById(ul_id);
  // Mock func to return array of dummy names
  const names = await mockAPI(num);

  // For each name in names append it to the ul element
  names.forEach((name) => {
    const li = document.createElement("li");
    li.innerText = name;
    ul.appendChild(li);
  });
}
```

#### Combining all the Code

```js
import mockAPI from "./mockAPI";

const loadBtn = document.getElementById("load");

async function addNamesToList(ul_id, num) {
  const ul = document.getElementById(ul_id);
  // Mock func to return array of dummy names
  const names = await mockAPI(num);

  // For each name in names append it to the ul element
  names.forEach((name) => {
    const li = document.createElement("li");
    li.innerText = name;
    ul.appendChild(li);
  });
}

(function () {
  addNamesToList("ul", 50);

  // Observe loadBtn
  const options = {
    // Use the whole screen as scroll area
    root: null,
    // Do not grow or shrink the root area
    rootMargin: "0px",
    // Threshold of 1.0 will fire callback when 100% of element is visible
    threshold: 1.0
  };

  const observer = new IntersectionObserver((entries) => {
    // Callback to be fired
    entries.forEach((entry) => {
      // Only add to list if element is coming into view not leaving
      if (entry.isIntersecting) {
        addNamesToList("ul", 10);
      }
    });
  }, options);

  observer.observe(loadBtn);
})();

loadBtn.onclick = () => {
  addNamesToList("ul", 10);
};
```

In addition to the IntersectionOberserver we have also added an onClick handler to the load more names button. As a fallback, if the IntersectionOberserver doesn't work then the user can also click on the button to load more names.

## Demo and Complete Code

### Code Sandbox

<iframe src="https://codesandbox.io/embed/infinite-scrolling-5ofl9?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="Infinite Scrolling"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

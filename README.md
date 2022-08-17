# Xbox Series X/S with HTML/CSS

In this small demo you can choose between both Xbox Series models. When you select the unselected model a transition occurs. Moving your cursor to either side of the model will slightly change the view.

You can find the demo here (looks much better in its full glory here):
https://codepen.io/tumain/full/poyqVeb

{% codepen https://codepen.io/tumain/pen/poyqVeb %}

I thought it would be interesting to cover how I created the demo and some of its visual aspects; including:
* Cube creation
* Transitions between models
* Patterns on models
* Xbox logo creation
* 3D movement on cursor position

## Cube creation
To begin, I looked at how to create a cube. I used [this guide](https://davidwalsh.name/css-cube) to construct one. I recommend reading the article if you're unfamilar with CSS cube creation, but in short; I created a `.cube` class. This `.cube` class has six elements within, each representing a face of the cube: top, right, bottom, left, back, front. Altering the translation and rotation of each cube face via 3D transforms, allowed me to create a cube.

I then recorded the dimensions of each Xbox and sampled each of their colours from the image below.

Each model would share the same CSS variable that would be changed upon transition; so I created CSS variables to store this data.

```css
:root {
    --height: 55vw;
    --width: 30.2vw;
    --depth: 12.6vw;
    --seriess: #E7E7E7;  // Series S background colour
    --seriesx: #1F1E25;  // Series X background colour
    --view: -222deg;     // View of the scene
}
```
I updated the existing CSS widths and heights I used from the guide, to use these CSS variables. I then tweaked the 3D transform of the model until I was satisfied with the view.

## Transitions between both models

Loaded with the colours and dimensions of each model, I created two classes, putting the colours per face of the Xbox (box) model. One for `.series-s` and the other for `.series-x`. This class is applied to the `body` tag, depending which model is selected.

The next part of the puzzle was to update the CSS variables depending on which model was selected. I executed this by using JS' `style.setProperty` method. For example, if I wanted to change the width (`--width`) of the model on screen I would do this:

```js
document.documentElement.style.setProperty("--width", NEW_WIDTH_HERE + "vw");
```

In my JS I store an object holding the dimensions of each model. Here's an example of the Series S.

```js
let seriesS = {
  height: 55,
  width: 30.2,
  depth: 12.6
};
```
I created a function where you can pass in these properties and it updates the dimensions of the model.
```js
let setProperties = (props) => {
  document.documentElement.style.setProperty("--width", props.width + "vw");
  document.documentElement.style.setProperty("--height", props.height + "vw");
  document.documentElement.style.setProperty("--depth", props.depth + "vw");
};
```
To trigger this I made a clickable X and S element at the bottom of the page. If I wanted the S model, I simply call the `setProperties` method with the `seriesS` object and remove the current class from the `body` tag and add the class I wanted; `series-s`.

```js
let seriesSSelected = () => {
  setProperties(seriesS);
  document.body.classList.add("series-s");
  document.body.classList.remove("series-x");
};
```
After getting the transition between colours and size working, I added the visual elements of each model in. 

On the S model there is a large black circular vent using the class `.circle`. This is simply a black circle (`border-radius: 50%`) absolutely positioned on the front face of the model.

To achieve the transition where it shrinks when the X model is selected I created a `x-scale-0` class. This class is a child of `.series-x` and simply sets the scale of the element to 0. So when the Series X is selected the scale down occurs.

```css
.series-x .x-scale-0 {
   transform: scale(0);
}
```
Likewise there is a `.s-scale-0` class, which works the other way.


## Patterns on models
At the top of the S and X models there are circular vents. There are also circular vents on the S' model front. To achieve this pattern I used a background; utilising `radial-gradient` and `background-size`.

The following is used for the S' front circles.

```css
background-size: .9vw .9vw;
background-image: radial-gradient(#000 50%, transparent 50%);
```

I tweaked the background size to increase/decrease the size of the circles, depending on the scenario.

## Xbox logo creation
The Xbox logo is made up of three circles:
1. Perfectly round circle, used for the background
2. Nested in 1; a transparent shape with a border applied and differing width and heights
3. Same as 2 but placed in a different position

For points 2 and 3, I tweaked the width and height a lot to get the desired outcome.

## 3D movement on cursor position
I added this at the last minute just to show off that it is 3D. This uses the CSS variable `--view`, that we mentioned at the start.

I firstly added event listeners onto the body, tracking `mousemove` and `mouseleave`. `mousemove` slightly changes the CSS `--view` variable depending on the cursor position; whereas `mouseleave` resets the `--view` to its initial variable.

```js
// the scene's initial rotation value
let initialView = -222;

// move rotation on mouse movement
let onMouseMove = (e) => {
// calculate percentage of the cursor's x position
// e.pageX: cursor position
// window.innerWidth: screen width
  xPercent = parseInt((e.pageX / window.innerWidth) * 100) - 75;
// add the movement to the initial view
  var view = initialView;
  view += xPercent / 2;
// update the --view CSS variable
  document.documentElement.style.setProperty("--view", view + "deg");
};
```
Hopefully the commented code above makes sense. The value '75' was used because it felt like a healthy offset to move the camera to the left or right.

The mouse leave event just resets the model to its initial view, so when the cursor goes off the screen the view resets.
```js
let onMouseLeave = (e) => {
  document.documentElement.style.setProperty("--view", initialView + "deg");
};
```
Then we also need to add the event listeners.
```js
let b = document.body;
b.addEventListener("mousemove", onMouseMove);
b.addEventListener("mouseleave", onMouseLeave);
```
## Udacity FEND P4 Website Optimization

This project consists of 2 parts
1. Part1: PageSpeed Score  
1. Part2: Getting Rid of Jank

## Part 1: Optimize PageSpeed Insights score for index.html

### Environment and score
1. I use [fenix](http://fenixwebserver.com/)
to build a server.
1. PageSpeed Insights mobile/laptop score is 91/94(my last reviewer get 99 in laptop)
1. install[FileOptimizer](http://nikkhokkho.sourceforge.net/static.php?page=FileOptimizer)

### Critical Rendering Path
1. **minify** both style.css and print.css file
1. **inline** both style.min.css and print.min.css file
1. set **media print** print.min.css file
1. move all the **script** tags to the **end** of **body** tag.
1. delete http*.js file
1. **optimize** these 4 **images** with [FileOptimizer](http://nikkhokkho.sourceforge.net/static.php?page=FileOptimizer) and **resize** the pizzeria.jpg with[ImageMagic Display] (http://www.imagemagick.org/script/binary-releases.php)

## Part2: Getting Rid of Jank

1. **minify** bootstrap-grid.css, style.css and main.js 3 files
1. prefix my .css files with [vendor prefix](http://autoprefixer.github.io/)

### Frame Rate
1. "use strict" mode at the beginning of main.js.
1. replace querySelector by getElementById
```
var pizzasize = document.getElementById("pizzaSize").innerHTML;
```
1. replace querySelectorAll by getElementsByClassName
```
var randomPizzas = document.getElementsByClassName("randomPizzaContainer");
```
1. modify the for loop, avoid checking randomPizzas.length value at each iteration
```
for (var i = 0, randompizzalength = randomPizzas.length; i < randompizzalength; i++) {
  randomPizzas[i].style.width = newwidth + '%';
}
```
1. declare the pizzasDiv variable outside the loop
```
var pizzasDiv = document.getElementById("randomPizzas");
for (var i = 2; i < 100; i++) {
  pizzasDiv.appendChild(pizzaElementGenerator(i));
}
```
1. Declaring the phase variable (var phase;) in the initialisation of the for loop to prevent it from being created every time the loop is executed.
```
for (var i = 0, itemslength = items.length, phase; i < itemslength; i++) {
  phase = phaseall[i % 5];
```
1. modify **updatePositions** function
1. 
```
var docscroll = document.body.scrollTop / 1250;
var phaseall = [];
for (var i = 0; i < 5; i++) {
  var mathdocscro = Math.sin(docscroll + i);
  phaseall.push(mathdocscro);
}
```
  create a new loop to compute scrollTop outside the original for-loop which can avoid repeating layout-style pipeline.
  use i as an index to create the new loop
1. 
```
var itemslength = items.length;
for (var i = 0; i < itemslength; i++) {
  var phase = phaseall[i % 5];
  items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
}
```
outside the items.length, call the result calculated before and batch modify style of items

### Computational Efficiency
1. replace document.querySelectorAll(".randomPizzaContainer") with a new variable randomPizzas.
1. not only update the **variable**, but also update the **DOM** manually
```
function changeSliderLabel(size) {
  var pizzasize = document.getElementById("pizzaSize").innerHTML;
  switch(size) {
    case "1":
      pizzasize = "Small";
      document.getElementById("pizzaSize").innerHTML = pizzasize;
      return;
    case "2":
      pizzasize = "Medium";
      document.getElementById("pizzaSize").innerHTML = pizzasize;
      return;
    case "3":
      pizzasize = "Large";
      document.getElementById("pizzaSize").innerHTML = pizzasize;
      return;
    default:
      console.log("bug in changeSliderLabel");
  }
}
```
1. modify **changePizzaSizes** function and delete **determineDx** function,
add the **var newwidth;** at the top of the function.
```
function changePizzaSizes(size) {
  var newwidth;
  switch(size) {
    case "1":
      newwidth = 25;
      break;
    case "2":
      newwidth = 33.3;
      break;
    case "3":
      newwidth = 50;
      break;
    default:
      console.log("bug in sizeSwitcher");
  }

  var randomPizzas = document.querySelectorAll(".randomPizzaContainer");

  for (var i = 0; i < randomPizzas.length; i++) {
    randomPizzas[i].style.width = newwidth + '%';
  }
}
```
read the layout and batch change the style.
1. The number of background pizzas is modified. Recaculate the columns and rows of pizza. And get the number of it by multiplying cols and rownum.
```
document.addEventListener('DOMContentLoaded', function() {
  var s = 256;
  var movingpizza1 = document.getElementById("movingPizzas1");

  var screenheight = window.innerHeight;
  var screenwidth = window.innerWidth;
  var rownum = Math.round(screenheight / s);
  var cols = Math.round(screenwidth / s);
  var elenum = rownum * cols;

  for (var i = 0, elem; i < elenum; i++) {
    elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizza.png";
    elem.style.height = "100px";
    elem.style.width = "73.333px";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    movingpizza1.appendChild(elem);
  }
  updatePositions();
});
```
1. Declaring the elem variable (var elem;) in the initialisation of the for-loop to prevent it from being created every time the loop is executed.
```
for (var i = 0, elem; i < elenum; i++) {
  elem = document.createElement('img');
```
1. replace the querySelector by document.getElementById() and move this DOM call outside the for statement and save it into a local variable, reference inside the for statement.

```
/**Outside the loop */
var movingPizzas = document.getElementById('movingPizzas1');

/** Inside the for statement / loop – this line*/
movingPizzas.appendChild(elem);
```
1. increase sites performance with hardware accelerated CSS
```
-webkit-transform: translateZ(0);
        transform: translateZ(0);
-webkit-transform: translate3d(0,0,0);
        transform: translate3d(0,0,0);
-webkit-backface-visibility: hidden;
        backface-visibility: hidden;
```

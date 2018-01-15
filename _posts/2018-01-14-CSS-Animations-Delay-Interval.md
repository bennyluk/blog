---
layout: post
title: CSS Animation with Delay Between Intervals.
description: One of the weird quirky things I found within the CSS Animation Library is the misleading, [animation-delay property](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-delay). I encountered this issue when I was working to create delay between intervals on CSS animated SVG’s.
tags: css animation hackaround
---

<style>
  #css-animation-delay-demo-1 {
    text-align: center;
    margin: 25px 0;
  }

  #css-animation-delay-demo-1 .demo {
    display: inline-block;
    margin: 25px;
  }

  #css-animation-delay-demo-1 .demo:first-child .fa-star {
    -webkit-animation-delay: 2s;
    -moz-animation-delay: 2s;
    -o-animation-delay: 2s;
    animation-delay: 2s;
    -webkit-animation-iteration-count: infinite;
    -moz-animation-iteration-count: infinite;
    -o-animation-iteration-count: infinite;
    animation-iteration-count: infinite;
  }

  #css-animation-delay-demo-1 .run-demo-btn {
    display: block;
    margin: 10px auto;
  }
</style>

### Almost There

One of the weird quirky things I found within the CSS Animation Library is the misleading, [animation-delay property](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-delay). I encountered this issue when I was working to create delay between intervals on CSS animated SVG’s.

Here is an example of the animation-delay. It seems that the animation will only be delayed on the “first time”. After that first delay, the next iteration will automatically play after the last animation.

<div id="css-animation-delay-demo-1">
  <div class="demo">
    <i class="fa fa-star fa-fw fa-2x" aria-hidden="true"></i>
    <div>Using Native animation-delay</div>
    <div>Only Delay Animation on First iteration</div>
  </div>
  <div class="demo">
    <i class="fa fa-star fa-fw fa-2x" aria-hidden="true"></i>
    <div>JS Solution.</div>
    <div>Delay Animation on **All** iteration</div>
  </div>
  <button class="run-demo-btn">Run Demo</button>
</div>
```css
.star {
  animation-delay: 2s;
  animation-iteration-count: infinite;
}
```
### Solution #1 - Adjusting Timeframes

This is probably the most common solution I found online that fix this issue. Not only is it well commonly used, but also very reliable as this method is not a “hack-around” but more of a “work-around”.

This solution requires adding that extra delay to the timeframe, so basically the delay is pretty much in the animation itself.

The only downfall to this issue I can find, is that it’s much harder to maintain as the delay is part of the animation. Also, if you were to use a [CSS animation library](https://daneden.github.io/animate.css/), it’s pretty much impossible without adjusting the existing timeframes.

If you are interested, check out [CSS Tricks article](https://css-tricks.com/css-keyframe-animation-delay-iterations/).

### Solution #2 - AnimationEnd Event Listeners

CSS Animations have a quite hidden event called the [AnimationEnd Event](https://developer.mozilla.org/en-US/docs/Web/Events/animationend). As the name suggests, this event is called once when the CSS animation ends. This way will ensure that the animation is complete, before we start writing the delay logic.

This solution breaks down in 3 major components. Animation CSS Class, AnimationEnd Event, and a [JS Timeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout).

Create a CSS class only containing the animation attributes.

```css
.animate {
  animation: myAnimation;
}
```

On the Javascript side, simply write a event listener for the AnimationEnd. Insert a Timeout, and toggle the animate class.

```js
// Using jQuery to make it easier

var delay  = 2000; // delay duration in ms.
var eventNames = "webkitAnimationEnd mozAnimationEnd animationend"; // event name, support webkit and moz.
var $element = $(".animate-element"); // element that you wanted to be animated.

$element.on(eventNames, function() { // add event listener

  $element.removeClass("animate"); // remove the existing animation. used to reset the animation

  setTimeout(function() { // using setTimeout to create the delay.

    $element.addClass("animate"); // add the animation class, to start animation

  }, delay);
});
```

### Solution #3 - JS Intervals

Lastly, another of method of tackling this problem is to mix [JS Intervals](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) with JS Timeout. Although this solution will work fine, I personally don't like to mix intervals with timeout. There is also an issue with browser does not support HTML5 CSS Animation, the interval will keep on running without actually doing anything, which is just created more resources.

```js
// Using jQuery to make it easier

var delay  = 2000; // delay duration in ms.
var $element = $(".animate-element"); // element that you wanted to be animated.

setInterval(function() { // loop the function (the delay)

  $element.removeClass("animate"); // remove the existing animation. used to reset the animation

  setTimeout(function() { // using setTimeout to create the delay.

    $element.addClass("animate"); // add the animation class, to start animation

  })
}, delay);
```

<script>
  $(function() {
    var $demo1 = $("#css-animation-delay-demo-1");
    var demo1JSDelayTimeout = null;

    var $demo1Btn = $demo1.find(".run-demo-btn").click(function() {
      $demo1.find(".demo .fa-star").removeClass("tada animated");
      $demo1.find(".demo:nth-child(2)").off("animationend");
      clearTimeout(demo1JSDelayTimeout);

      if($demo1Btn.text() === "Run Demo") {
        $demo1.find(".demo .fa-star").addClass("tada animated");

        var $demo1JSDelay = $demo1.find(".demo:nth-child(2) .fa-star").on("animationend", function() {
          $demo1JSDelay.removeClass("tada animated");
          demo1JSDelayTimeout = setTimeout(function() {
            $demo1JSDelay.addClass("tada animated");
          }, 2000);
        });

        $demo1Btn.text("Stop Demo");
      } else {
        $demo1Btn.text("Run Demo");
      }
    });
  });
</script>

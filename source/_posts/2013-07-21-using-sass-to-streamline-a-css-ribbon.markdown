---
layout: post
title: "Using Sass to streamline a css ribbon."
date: 2013-07-21 14:08
comments: true
categories: 
---

For a simple listing page I was working on, I needed some sort of ribbon to depict a status for that listing item. Having seen lots of ribbons on various web pages I went shopping for some pre-made nice looking ribbons. I was using [Sass](http://sass-lang.com) as a css preprocessor and [sass-bootstrap](https://github.com/thomas-mcdonald/bootstrap-sass) for rapid prototyping so sure enough, the first google result for `sass bootstrap ribbon` yielded a [rejected bootstrap issue](https://github.com/twitter/bootstrap/issues/5550) with a working [jsfiddle](http://jsfiddle.net/UBfCE/2/) for a nice green ribbon.

This is the css on that fiddle:

``` css
.ribbon-wrapper {
  width: 85px;
  height: 88px;
  overflow: hidden;
  position: absolute;
  top: -3px;
  right: -3px;
  z-index: 9999;
}

.ribbon {
  font: bold 15px Sans-Serif;
  color: #333;
  text-align: center;
  text-shadow: rgba(255,255,255,0.5) 0px 1px 0px;
  -webkit-transform: rotate(45deg);
  -moz-transform:    rotate(45deg);
  -ms-transform:     rotate(45deg);
  -o-transform:      rotate(45deg);
  position: relative;
  padding: 7px 0;
  left: -5px;
  top: 15px;
  width: 120px;
  background-color: #BFDC7A;
  background-image: -webkit-gradient(linear, left top, left bottom, from(#BFDC7A), to(#8EBF45)); 
  background-image: -webkit-linear-gradient(top, #BFDC7A, #8EBF45); 
  background-image:    -moz-linear-gradient(top, #BFDC7A, #8EBF45); 
  background-image:     -ms-linear-gradient(top, #BFDC7A, #8EBF45); 
  background-image:      -o-linear-gradient(top, #BFDC7A, #8EBF45); 
  color: #6a6340;
  -webkit-box-shadow: 0px 0px 3px rgba(0,0,0,0.3);
  -moz-box-shadow:    0px 0px 3px rgba(0,0,0,0.3);
  box-shadow:         0px 0px 3px rgba(0,0,0,0.3);
}

.ribbon:before, .ribbon:after {
  content: "";
  border-top:   3px solid #6e8900;   
  border-left:  3px solid transparent;
  border-right: 3px solid transparent;
  position:absolute;
  bottom: -3px;
}

.ribbon:before {
  left: 0;
}
.ribbon:after {
  right: 0;
}
```

And it produces a nice looking ribbon stuck to the top right of the page:

![Original ribbon](/images/ribbon1.png)

With some minor modifications I adapted the css to apply the ribbon to every item on the listing instead of the whole page. Also, I needed several colored ribbons representing different listing states. The result was pretty spiffy:

![Active ribbon](/images/ribbon2.png)
![Deleted ribbon](/images/ribbon3.png)
![Inactive ribbon](/images/ribbon4.png)

But the css was growing to be a huge mess of repeated and inflexible code. For each new color needed, I had to find the right gradient and text colors, and repeat some css in an ever growing exercise of copy/paste. This was becomming pretty ugly, pretty fast:

```css
.ribbon-wrapper {
  width: 106px;
  height: 93px;
  overflow: hidden;
  position: absolute;
  z-index: 9999;
}

.ribbon {
  font: bold 15px Sans-Serif;
  color: #333;
  text-align: center;
  text-shadow: rgba(255,255,255,0.5) 0px 1px 0px;
  -webkit-transform: rotate(-45deg);
  -moz-transform:    rotate(-45deg);
  -ms-transform:     rotate(-45deg);
  -o-transform:      rotate(-45deg);
  position: relative;
  padding: 7px 0;
  left: -28px;
  top: 17px;
  width: 120px;
  -webkit-box-shadow: 0px 0px 3px rgba(0,0,0,0.3);
  -moz-box-shadow:    0px 0px 3px rgba(0,0,0,0.3);
  box-shadow:         0px 0px 3px rgba(0,0,0,0.3);

}

.active {
  color: darken(#8EBF45, 30);
  background-color: #BFDC7A;
  background-image: -webkit-gradient(linear, left top, left bottom, from(#BFDC7A), to(#8EBF45)); 
  background-image: -webkit-linear-gradient(top, #BFDC7A, #8EBF45); 
  background-image:    -moz-linear-gradient(top, #BFDC7A, #8EBF45); 
  background-image:     -ms-linear-gradient(top, #BFDC7A, #8EBF45); 
  background-image:      -o-linear-gradient(top, #BFDC7A, #8EBF45); 
}
.refused {
  color: darken(#BD362F, 30);
  background-color: #EE5F5B;
  background-image: -webkit-gradient(linear, left top, left bottom, from(#EE5F5B), to(#BD362F)); 
  background-image: -webkit-linear-gradient(top, #EE5F5B, #BD362F); 
  background-image:    -moz-linear-gradient(top, #EE5F5B, #BD362F); 
  background-image:     -ms-linear-gradient(top, #EE5F5B, #BD362F); 
  background-image:      -o-linear-gradient(top, #EE5F5B, #BD362F); 
}
.inactive {
  color: darken(#E6E6E6, 30);
  background-color: #FFFFFF;
  background-image: -webkit-gradient(linear, left top, left bottom, from(#FFFFFF), to(#E6E6E6)); 
  background-image: -webkit-linear-gradient(top, #FFFFFF, #E6E6E6); 
  background-image:    -moz-linear-gradient(top, #FFFFFF, #E6E6E6); 
  background-image:     -ms-linear-gradient(top, #FFFFFF, #E6E6E6); 
  background-image:      -o-linear-gradient(top, #FFFFFF, #E6E6E6); 
}

.ribbon:before, .ribbon:after {
  content: "";
  border-top:   3px solid #6e8900;   
  border-left:  3px solid transparent;
  border-right: 3px solid transparent;
  position:absolute;
  bottom: -3px;
}

.ribbon:before {
  left: 0;
}
.ribbon:after {
  right: 0;
}
```

#### Sass to the rescue
Let's refactor this code using the some nice [Sass](http://sass-lang.com) features and some ready-made mixins provided by sass-bootstrap.

#### Removing vendor prefixes
The low hanging fruit is the vendor prefixes that we can reduce by using some sass-bootstrap mixins. You can get a list of bootstrap provided mixins in [the _mixins.scss file](https://github.com/thomas-mcdonald/bootstrap-sass/blob/master/vendor/assets/stylesheets/bootstrap/_mixins.scss). Mixins are like modules of code that can be included in other rules. They can even take arguments to further customize the included code. Take for example the sass-bootstrap `rotate` mixin:

```scss
@mixin rotate($degrees) {
  -webkit-transform: rotate($degrees);
     -moz-transform: rotate($degrees);
      -ms-transform: rotate($degrees);
       -o-transform: rotate($degrees);
          transform: rotate($degrees);
}
```
This scss syntax defines a mixin `rotate` that takes the variable `$degrees` as an argument . When you include this mixin in another rule like this:
```scss
.diagonal {
  color: white;
  @include rotate(-45deg);
}
```
the mixin gets expanded (mixed in) in that rule:
```css
.diagonal {
  color: white;
  -webkit-transform: rotate(-45deg);
     -moz-transform: rotate(-45deg);
      -ms-transform: rotate(-45deg);
       -o-transform: rotate(-45deg);
          transform: rotate(-45deg);
}
```

So using the available sass-bootstrap mixins, these rather verbose rules

```css
  -webkit-transform: rotate(-45deg);
  -moz-transform:    rotate(-45deg);
  -ms-transform:     rotate(-45deg);
  -o-transform:      rotate(-45deg);

  -webkit-box-shadow: 0px 0px 3px rgba(0,0,0,0.3);
  -moz-box-shadow:    0px 0px 3px rgba(0,0,0,0.3);
  box-shadow:         0px 0px 3px rgba(0,0,0,0.3);

  background-image: -webkit-gradient(linear, left top, left bottom, from(#FFFFFF), to(#E6E6E6)); 
  background-image: -webkit-linear-gradient(top, #FFFFFF, #E6E6E6); 
  background-image:    -moz-linear-gradient(top, #FFFFFF, #E6E6E6); 
  background-image:     -ms-linear-gradient(top, #FFFFFF, #E6E6E6); 
  background-image:      -o-linear-gradient(top, #FFFFFF, #E6E6E6); 
```

can be replaced by these that will generate the same output

```scss
  @include rotate(-45deg);
  @include box-shadow(0px 0px 3px rgba(0,0,0,0.3));
  @include gradient-vertical(#FFFFFF, #E6E6E6);
```

#### Creating a mixin for the ribbon color

One annoyance I faced when adding other ribbon colors for different status, was that I needed to pick three colors to define the new ribbon: a base color, a lighter color for the gradient and a darker color for the text and ribbon folds. Sass provides some functions to do color math, like `lighten` and `darken` that we could use to avoid having to specify three different colors. 
So I created a mixin that receives a base color, calculates both a lighter and darker versions of that color and assigns the resulting colors to variables that are used in the relevant places. Also used the `&` operator to reference the parent selector of the rule this mixin will be included in.

```scss
  @mixin ribbon-color($ribbon_base_color) {
    $ribbon_darker_color: darken($ribbon_base_color, 30);
    $ribbon_lighter_color: lighten($ribbon_base_color, 20);

    color: $ribbon_darker_color;
    @include gradient-vertical($ribbon_lighter_color, $ribbon_base_color);
    &:before, &:after {
      border-top:   3px solid $ribbon_darker_color;   
    }
  }
```

With this mixin we can define a ribbon color just by specifying the base color like this:

```scss
  &.active {
    @include ribbon-color(#8EBF45);
  }
```

The final result is much more manageable.

```scss
.ribbon-wrapper {
  width: 106px;
  height: 93px;
  overflow: hidden;
  position: absolute;
  z-index: 9999;
}

.ribbon {
  font: bold 15px Sans-Serif;
  color: #333;
  text-align: center;
  text-shadow: rgba(255,255,255,0.5) 0px 1px 0px;
  position: relative;
  padding: 7px 0;
  left: -28px;
  top: 17px;
  width: 120px;
  @include rotate(-45deg);
  @include box-shadow(0px 0px 3px rgba(0,0,0,0.3));

  @mixin ribbon-color($ribbon_base_color) {
    $ribbon_darker_color: darken($ribbon_base_color, 30);
    $ribbon_lighter_color: lighten($ribbon_base_color, 20);

    color: $ribbon_darker_color;
    @include gradient-vertical($ribbon_lighter_color, $ribbon_base_color);
    &:before, &:after {
      border-top:   3px solid $ribbon_darker_color;   
    }
  }

  &.active {
    @include ribbon-color(#8EBF45);
  }
  &.refused {
    @include ribbon-color(#BD362F);
  }
  &.inactive {
    @include ribbon-color(#E6E6E6);
  }
  &.deleted {
    @include ribbon-color(#ff780b);
  }
  &:before, &:after {
    content: "";
    border-left:  3px solid transparent;
    border-right: 3px solid transparent;
    position:absolute;
    bottom: -3px;
  }
  &:before {
    left: 0;
  }
  &:after {
    right: 0;
  }

}
```

There are probably other improvements to this code, but I'm pretty happy with the results. Once you start using a css preprocessor like Sass, and realizing its power you cannot go back to plain css anymore. 

title: Loops and Mixins in SASS
date: 2014-04-05 16:29:18
tags: [sass, smacss]
categories: Front End Development
---

The beauty of freelancing is twofold: you work on a lot of different projects simultaneously, and have to learn to quickly dive into a various set of systems at any given moment.

One challenge I run into in my work is building scalable, modular CSS, and lately I've been making a strong effort to write more modular code.

Here's a pattern I ran into today. Say I want to add a simple spacer class that adds a little padding or margin on an element. I'd rather not have to do anything beyond editing the HTML markup, so let's generate some CSS classes using a SASS `@each` loop.

My first take was to set up some `$spaceamounts`, then generate a few mixins:

``` sass helpers.sass

$spaceamounts: (5, 10, 15, 20, 25, 30);

@mixin generate-margin-bottom() {
  @each $space in $spaceamounts {
    .mb-#{$space} {
      margin-bottom: #{$space}px;
    }
  }
}
@mixin generate-margin-right() {
  @each $space in $spaceamounts {
    .mr-#{$space} {
      margin-right: #{$space}px;
    }
  }
}
@mixin generate-margin-top() {
  @each $space in $spaceamounts {
    .mt-#{$space} {
      margin-top: #{$space}px;
    }
  }
}
@mixin generate-padding-top() {
  @each $space in $spaceamounts {
    .pt-#{$space} {
      padding-top: #{$space}px;
    }
  }
}
@mixin generate-padding-bottom() {
  @each $space in $spaceamounts {
    .pb-#{$space} {
      padding-bottom: #{$space}px;
    }
  }
}

@include generate-margin-bottom();
@include generate-margin-right();
@include generate-margin-top();
@include generate-padding-bottom();
@include generate-padding-top();

```

This approach definitely works. I get some sensibly generated CSS:

``` css application.css

.ml-5 {
  margin-left: 5px; }

.ml-10 {
  margin-left: 10px; }

.ml-15 {
  margin-left: 15px; }

...

```

But, that's a lot of copying and pasting. How about optimizing a bit?

With the relatively new `@each` loop and nested array functions in SASS, you can set comma delimited lists for use in dynamic variables.

``` sass helpers.sass

// let's generate some CSS!
// loops through array:
// vars: amt, direction, class-suffix
$default-space-amounts-with-direction: (5 left l, 10 left l, 15 left l, 25 left l, 30 left l);

```

We set up a basic iteration loop, here. The goal is to generate markup that follows our pattern from above, which fortunately is fairly simple:

``` sass helpers.sass

@mixin generate-spacing-classes(
  $default-space-amounts-with-direction: (5 left l, 10 left l, 15 left l, 25 left l, 30 left l)
) {
  @each $space in $default-space-amounts-with-direction {
    .m#{nth($space, 3)}-#{nth($space, 1)} {
      margin-#{nth($space, 2)}: #{nth($space, 1)}px;
    }
  }
}

@include generate-spacing-classes();

```

This generates the exact same markup as before.

Boom. Now, I can add more variable array definitions and simply include a few lines to have it output for each of these loops:

``` sass helpers.sass

$right-space-vars: (5 right r, 10 right r, 15 right r, 25 right r, 30 right r);
$bottom-space-vars: (5 bottom b, 10 bottom b, 15 bottom b, 25 bottom b, 30 bottom b);
$top-space-vars: (5 top t, 10 top t, 15 top t, 25 top t, 30 top t);

@include generate-spacing-classes(); // left comes by default
@include generate-spacing-classes($right-space-vars);
@include generate-spacing-classes($bottom-space-vars);
@include generate-spacing-classes($top-space-vars);

```

Voila, we get this:

``` css application.css

.ml-5 {
  margin-left: 5px;
  background: teal !important; }

.ml-10 {
  margin-left: 10px;
  background: teal !important; }

.ml-15 {
  margin-left: 15px;
  background: teal !important; }

.ml-25 {
  margin-left: 25px;
  background: teal !important; }

.ml-30 {
  margin-left: 30px;
  background: teal !important; }

.mr-5 {
  margin-right: 5px;
  background: teal !important; }

.mr-10 {
  margin-right: 10px;
  background: teal !important; }

.mr-15 {
  margin-right: 15px;
  background: teal !important; }

...

```

This entire approach follows the pattern and way I like to work is to build principles and a foundation so each project is a bit easier.

It's kind of like climbing a mountain a little faster each time.
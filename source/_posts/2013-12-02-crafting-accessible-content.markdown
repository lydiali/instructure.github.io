---
layout: post
title: "Crafting accessible content"
author: Zach Pendleton
date: 2013-12-02 09:52
comments: true
categories:
- accessibility
- javascript
- html
---

At Instructure, we're moving more and more of our app to the client with technologies like [Ember.js](http://emberjs.com). While it and other JavaScript frameworks have made this transition easier, they've also introduced new challenges around keeping our content accessible.

The old advice we've all heard about using semantic markup, headers, and `alt` attributes on `<img />` tags is still true, but it doesn't address how we can best develop accessible content in a rich client application. Despite rumors to the contrary, rich content _can_ be accessible if we pay attention to a few basics.

<!--more-->

## Getting started with accessibility

{% blockquote W3 http://www.w3.org/standards/webdesign/accessibility %}
The Web is fundamentally designed to work for all people, whatever their hardware, software, language, culture, location, or physical or mental ability. When the Web meets this goal, it is accessible to people with a diverse range of hearing, movement, sight, and cognitive ability.
{% endblockquote %}

Let's start with a few definitions:

- **Accessibility**: Web content is accessible when it usable by persons with disabilities. While this includes users of screen readers, it also includes persons with cognitive and other disabilities.
- **WAI-ARIA**: A suite of technologies designed to make rich Internet applications accessible to users with disabilities.

Authoring accessible rich content most often means (1) using WAI-ARIA attributes such as `role` and `aria-expanded` to mark-up content in a machine-parseable way; (2) ensuring that all content is keyboard navigable; and (3) managing page focus.

So what does all of this mean when you're developing widgets? Let's take a look at an example of a button set component.

## Building a button set

We're going to build an accessible [button set](http://getbootstrap.com/components/#btn-groups) using Twitter's Bootstrap styles and jQuery.

Our button set is going to work like a filter, so users can only select one button at a time. This means it's like a radio button. Watch how we can use aria roles and attributes to convey that behavior to non-sighted users:

```html
<div class="btn-group" role="radiogroup">
  <button type="button"
          class="btn btn-default active"
          role="radio"
          aria-checked="true">Open</button>
  <button type="button"
          class="btn btn-default"
          role="radio"
          aria-checked="false">Closed</button>
</div>
```

First, notice that we didn't change Twitter's recommended button set markup &mdash; we only augmented it with the `role` attributes and an `aria-checked` attribute. The `role` attribute describes the component's behavior to screen readers, and `aria-checked` attribute notifies users of the current option in the same way that the `active` class does for sighted users.

Here's the JavaScript to make this happen:

```javascript
$.fn.buttonset = function() {
  $(this).on('click', function(e) {
    var $target = $(this);

    $target.siblings()
      .removeClass('active')
      .attr('aria-checked', false);

    $target
      .addClass('active')
      .attr('aria-checked', true);
  });
};

// usage
$('.btn-group .btn').buttonset();
```

While we're defining this as a jQuery plugin, we could have just as easily made an Ember component, an Angular directive, or a Backbone view.

We've found that this level is often the best place for dealing with accessibility. By handling accessibility details in our reusable widgets and libraries we can code it once and benefit across our stack. New engineers can immediately start writing accessible code, and all engineers benefit from quick, accessible implementations of common patterns.

In this case, our code is almost all standard jQuery. This solution doesn't require us to manage focus or worry about keyboard navigation because buttons are already in the tab order, but often widget markup will need a `tabindex=-1` attribute and calls like `$('.item').focus()` to ensure that screen reader users aren't lost on the page.

Here's our component in action (it's easier to test with a screen reader if you pop out this example into its own page first):

<a class="jsbin-embed" href="http://jsbin.com/OBoqALa/1/embed?output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>

## Testing accessibility

If you're on a Mac, you already have the software you need to get started testing for accessibility. OSX ships with a screen reader, VoiceOver, that can be enabled in your System Preferences and activated by pressing Cmd-F5.

If you're on Windows, take a look at the free [NVDA](http://www.nvaccess.org/) or, if you're doing professional development, the more widely used [JAWS](http://www.freedomscientific.com/products/fs/jaws-product-page.asp).

To ensure that everything we write is accessible, we test all new commits in VoiceOver and JAWS as well as in our supported browsers before merging to master. And while giving accessibility top priority alongside visual fidelity and layout initially slowed us down, we've long since adjusted and now have  better code and a better product. 

## Wrapping up

Developing with accessibility in mind has been a great experience at Instructure. By starting with an eye to accessibility we've been able to leverage WAI-ARIA tools to create rich, accessible content in an unobtrusive way.

### Additional resources

To learn more about accessibility, take a look at the W3's [Authoring Practices](http://www.w3.org/TR/wai-aria-practices) document. It covers additional topics like keyboard shortcuts, managing focus, and properly labeling content. The Illinois Center of Information Technology and Web Accessibility also have a great [collection of accessibility examples](http://test.cita.uiuc.edu/aria/).

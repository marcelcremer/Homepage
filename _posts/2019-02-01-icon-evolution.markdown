---
title: The Icon evolution and "How I switched from Font based Icons to SVG"
layout: post
date: 2019-02-01 12:00
image: /assets/images/howi.png
headerImage: true
projects: false
#hidden: true # don't count this post in blog pagination
description: "A small journey through my personal learnings about web icons and how they are displayed."
tag: npm, javascript, node.js
category: blog
author: marcelcremer
externalLink: false
---

Last week, we made the decision to migrate from our font-based icon set to [feather icons](feathericons.com) - an SVG based font set. Since I've been using font based Icons like forever and had to read a bit about icons in general, I'm going to share what I learned with you. So please join me on a journey into...

### The Evolution of Icon Sets

A long time ago, in a world where CSS didn't exist, Icons were displayed via **images**.

And it was _good_.
Until...

People realisied, that images are very inflexible regarding their display size. Also, they consumed much bandwidth (which was costly) and if over-used, the website became slow (at that time, each image request blocked the main thread of the browser). But luckily, CSS arrived.

So people started to use so called **image sprites**, where a bunch of icons were placed on one image (which was cacheable). By positioning via CSS it was possible, to extract parts of this image sprite to just show the one icon needed in that particular place.

And it was _good_.
Until...

Web designers had again to struggle with images. Because people wanted to have the same icon in different colors and sizes, the image sprite grew larger and larger. Also accessibility became a problem.

So at around 2012, people got a new Idea: We can have text in different colors, scale it like we want to and even apply shadows on them. We can also draw text "above" other text by CSS positioning, so we could "craft" a new character by combining 2 others. And all that, by having a small file, which contains a definition on how to draw those Characters.

_But what if it didn't contain characters? What if it contained Icons?_

That's how **Font based Icon sets** were born. The icons were drawn carefully and delivered as a font set, where each "character" represented one icon.

They could be easily accessed by writing the corresponding character (e.g. via Unicode) or later via ligatures. They could be colored and sized, combined and even CSS effects were possible on them.

Although fonts weren't made for icons, the Icon Fonts lived a long and happy life.

And it was _good_.
Until...

### A small introduction in SVG

Scalable Vector graphics (short: SVG) are a standard to describe 2-dimensional Vector graphics in XML. According to [caniuse.com](https://caniuse.com/#search=SVG), all "modern" Browsers support them. Others can be polyfilled e.g. by [svg4everybody](https://github.com/jonathantneal/svg4everybody).

A simple example would be

```html
<svg>
    <circle cx="5" cy="5" r="4" fill="currentColor" />
</svg>
```

which paints a circle with the current color.

### Reasons to use SVG based Icon sets

Here's a (incomplete) list of benefits for SVG based Icons.

They are...

-   fast to paint
-   much smaller than traditional Image or font based icon sets
-   because they are basically code, they can be extended (e.g. by adding a rectangle into the above example)
-   consume less bandwidth, because they can be compressed for transfer (it's a text definition after all... :) )
-   They can even be used within a regular \<img> tag (although this limits your flexibility to adjusting width and height)
-   while Font based Icon positioning can be tricky (because they are inserted on a pseudo element, e.g. in CSS with :before and :after selector and the content-attribute), SVG based Icons can be just placed as an element itself and will be drawn nicely in their given dimensions
-   SVGs support Anti-Aliasing, which makes them in general a bit sharper than their font based counterparts.

### My Personal Decisions: JavaScript vs Angular implementation vs Sprites

When I started with the migration to SVG based Icons, what I first found was a JavaScript implementation that builds the SVG (first paragraph on [feathericons](https://github.com/feathericons/feather)). However, as I was working in an Angular SPA, I didn't want to use external JS and create overhead.

So my next "station" on the way to SVGs was, to check out if someone already built something for angular, and of course, someone did: [Angular Feather](https://github.com/michaelbazos/angular-feather) supports the import of single icons just as needed, where needed in the angular Application. Nice!

But wait... as every icon is encapsulated in an Angular component, I wouldn't have had access to the SVG and therefor, I wouldn't have been able to modify the color of Icon parts, add additional paths to the SVG or whatever else someone could imagine.

There had to be another option, and if I carefully read the rest of the Feather Icons site until the end, I would've seen it right at the beginning: **SVG Sprites**!

### SVG Sprites

SVG Sprites are basically an xml file, where many Icon definitions are stored with a corresponding anchor. In case of Feather Icons, the SVG sprite is about 50kb (uncompressed) which was very acceptable for my Web Application.

So I created the base class in css, which describes the overall icon behaviour:

```css
.fi {
    width: 1em;
    height: 1em;
    stroke: currentColor;
    stroke-width: 2;
    stroke-linecap: round;
    stroke-linejoin: round;
    fill: none;
}
```

Because I chose relative sizes, the icons are as high as they should be in the different places according to the font size (just like their font based counterparts).

The "stroke" defines how the lines defined by the SVG should be drawn. Because it's set to "currentColor", the icon will use the current font color of it's context.

Right after defining those basics, I was able to exchange my icons to their SVG-equivalent:

```html
<svg class="fi">
    <use xlink:href="/assets/feather/feather-sprite.svg#user" />
</svg>
```

(note #user, which is the anchor reference for the Icon I want to draw out of the SVG sprite)

And what should I say? **It just works!**

I was even able to create additional helper classes, for small, large and 2x 3x and 4x original size, by adding a few simple CSS classes.

I hope you could also take something out of my journey through the icon evolution. Which ones do you prefer and why?

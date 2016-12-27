These days the majority of people accessing any web site you build [will be using a small mobile touchscreen device](https://searchenginewatch.com/sew/opinion/2353616/mobile-now-exceeds-pc-the-biggest-shift-since-the-internet-began). But there will still be a significant minority who will access that same site from a laptop or desktop device with a much larger monitor. This poses an interesting design dilemma: how do we build one site that looks and works well on both a tiny phone screen as well as a gigantic desktop monitor?

There is no one magic formula for this, but there are some best practices, as well as some powerful CSS features that let us adjust our page's appearance and layout based on the screen size.

## Mobile-First Design

Because the majority of web traffic is now coming from mobile devices, the current best practice is to design your site for a mobile device first, and only later consider how you can take advantage of the larger screen of a desktop. This is known as "mobile-first design." In general this boils down to the following principles:

- **Layout**: Blocks of content should typically stack on top of each other on narrow screens, but sit side-by-side on wider screens. You can also use the `width` or `max-width` properties to constrain the overall width of your content so that it doesn't stretch to ridiculous line lengths on super-wide monitors. Content fixed to the viewport (`layout: fixed`) should be kept to a minimum on small screens, as it reduces the amount of scrollable screen real-estate.

- **Media**: Large images and video look great on large monitors, but typically don't work as well on small screens. They also take much longer to download over slow mobile links. Consider using background colors or linear gradient fills instead of background images on mobile; or use lower-resolution images so that they download faster.

- **Fonts**: Font sizes may need to be adjusted so that text remains readable as the screen size changes. You can also increase the size of headings or callout text on wider monitors to take advantage of the extra room.

- **Navigation**: Site navigation links take up a lot of room on small screens and may end up wrapping to multiple lines. Keep the site navigation hidden on small screens and show it only when the user taps a menu icon (colloquially known as the "hamburger icon"). Most CSS frameworks (which we will learn about next) provide some kind of collapsible navigation on mobile.

- **Input and Interaction**: Tap/click targets need to be large-enough on mobile to select using a finger, especially for people with poor eyesight or thick fingers. Tiny icons placed right next to one another, or one-word hyperlinks are difficult to select accurately. Specifying a data type on form fields (e.g., email address, phone number, date, integer) also generates optimized on-screen keyboards, making data entry much easier.

- **Content**: For some sites, you may even want to adjust what content is shown to mobile users as opposed to desktop users. For example, a phone number might become a large "call" icon with a `tel:` hyperlink on phone-sized screens, but simply appear as a normal telephone number on larger screens.

## Media Rules

All of the style rules we saw in the [Adding Some Style Tutorial](../adding-style) apply to all devices and screens regardless of size. But CSS now lets us define blocks of rules that apply only when the screen size is at least a particular width. We do this via [media rules](http://www.w3schools.com/cssref/css3_pr_mediaquery.asp), which look like this:

```css
/* rules that apply to all devices */
body {
    background-color: rgb(255,64,129); /* background color on small screens */
    font-size: 16px;                   /* default font size is 16 pixels */
}

/* rules that apply only to screens 768 pixels and wider */
@media (min-width: 768px) {
    body {
        background-image: url(...);   /* use background image on larger screens */
        font-size: 18px;              /* increase font size to 18 pixels on larger screens */
    }

    .mobile-call-icon {
        display: none;                /* hide mobile call icon */
    }
}
```

The `@media` rule defines a new block of style rules that apply only when the conditions of the `@media` rule are met. In this case, the `@media` rule condition is "the device width is at least 768 pixels." If the current device width is less than `768px`, the rules inside the block do not apply. If the width is equal to or greater than `768px`, they do.

All rules defined outside of a `@media` rule block will always apply to all screens. So you can define several rules at the top of your stylesheet that set your default formatting, and then adjust only the properties you need to change inside the `@media` rule block. Because they appear later, they override the same property setting in the earlier rules.

You can define as many `@media` rule blocks as you want in a stylesheet, each with a different condition, but most professionals define only a few that match the common breakpoints between phone, tablet, and desktop screen widths.

The rules inside the `@media` rule can alter the formatting of any element, including completely hiding or showing it using the `display` property. Setting `display: none` will remove the element from the page, and `display: block` or `display: inline` will show it.

## Flexbox

On narrow screens content blocks should typically stack on top of each other, but on wider screens we should take advantage of the extra room and display at least some content blocks side-by-side. In the past, we used the `float` and `clear` properties to achieve multi-column layouts, but this technique was fraught with bugs due to browser inconsistencies. Thankfully, there is a newer standard specifically designed for multi-column layouts: Flexbox. 

Flexbox requires one element to act as the row element, and one element per column in your layout. To make a two-column layout, your HTML and CSS would be like this:

```html
<div class="flex-row">
    <div class="flex-column">
        <h2>Column 1</h2>
        <p>...</p>
    </div>
    <div class="flex-column">
        <h2>Column 2</h2>
        <p>...</p>
    </div>
</div>
```

```css
* {
    box-sizing: border-box; /* include padding and border is size calcs */
}

.flex-row {
    display: flex;          /* start a flexbox */
}

.flex-column {
    flex-grow: 1;           /* all columns equal size */
    padding-left: 0.25em;   /* a little padding inside each col */
    padding-right: 0.25em;
}
```

<div class="flex-row example"><div class="flex-column"><h2>Column 1</h2><p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Odit libero, vero obcaecati pariatur reprehenderit nobis dolorum ratione. Reprehenderit deserunt esse quidem ratione pariatur suscipit voluptas illo ipsa consectetur, neque repellendus.</p></div><div class="flex-column"><h2>Column 2</h2><p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ad culpa laudantium perspiciatis voluptate laborum cumque consequatur corporis, quisquam in sapiente suscipit veniam itaque explicabo dolore eos similique, ea distinctio optio.</p></div></div>

The key properties here are `display: flex` and `flex-grow: 1`. The former starts a flexbox, which causes all child elements to be laid out side-by-side instead of stacked on top of each other. The latter tells the browser to grow each of those child elements by the same amount, so if there are two child elements, each will get 50% of the line width. If there are four, each will get 25% of the line width.

To play around with this, try my [Flexbox CodePen](https://codepen.io/drstearns/pen/vKVgrB). Try adding more `<div class="flex-column">` elements to the HTML to create more columns, and notice how the `flex-grow: 1` automatically divides the line width between all the columns inside the flexbox.

Flexbox is now well-supported by all of the major browsers, [except for IE](http://caniuse.com/#feat=flexbox). Thankfully, Microsoft's new Edge browser does support it, so this will be a short-term limitation. On IE, your content will still display, but it will always be stacked and never side-by-side. If you must provide multi-column layout to IE users, you should use the float-based grid system in one of the popular [CSS Frameworks](../css-frameworks/) (Bootstrap, Material Design Lite, Materialize, etc.).

## Box Sizing

By default, browsers will apply widths like `50%` to just the content within a box and not the padding or borders on the box. But when we start creating multi-column layouts, we need the column size to also include the padding we put on the left and right; otherwise, the overall size of the columns will add up to more than the viewport width, and the last column will wrap to the next line.

To force the browser to take the padding into consideration when sizing the column, we set `box-sizing` to `border-box`. This ensures that the content plus the padding and any border will be sized to 100% of the containing element. Here we use the `*` selector, which selects every element in the page, so this setting will apply to all elements. We could be more targeted and set `box-sizing` only on the `.flex-column` rule, but this setting is what you want most of the time on all elements when doing multi-column layouts, so it's common practice to just apply it to all elements using the `*` selector.

## Responsive Flexbox

The above example creates a multi-column layout on all screens, but we can make this responsive by adding some `@media` rule blocks, and adjusting a few properties. Let's start by making the columns stack on top of each other on small phone screens, but sit side-by-side on wider screens:

```css
* {
    box-sizing: border-box;
}

/* rules for screens 780px and wider */
@media (min-width: 780px) {
    .flex-row {
        display: flex;
    }

    .flex-column {
        flex-grow: 1;
        padding-left: 0.25em;
        padding-right: 0.25em;
    }
}
```

<div class="flex-row-md example"><div class="flex-column"><h2>Column 1</h2><p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Odit libero, vero obcaecati pariatur reprehenderit nobis dolorum ratione. Reprehenderit deserunt esse quidem ratione pariatur suscipit voluptas illo ipsa consectetur, neque repellendus.</p></div><div class="flex-column"><h2>Column 2</h2><p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ad culpa laudantium perspiciatis voluptate laborum cumque consequatur corporis, quisquam in sapiente suscipit veniam itaque explicabo dolore eos similique, ea distinctio optio.</p></div></div>

Note that we just moved the `flex-row` and `flex-column` rules into a `@media` rule that applies to screens `768px` and wider. On narrow screens, the `display: flex` won't be applied to the `<div class="flex-row">` element, so the columns will simply stack on top of each other, as `<div>` elements normally would.

## Multiple Layouts

What if we have four columns, and we want them all to stack on top of each other on narrow mobile screens, switch to a 2x2 layout on tablet screens, and switch to a 1x4 layout on wider desktop screens? We can do that by using a few more flexbox properties

```css
* {
    box-sizing: border-box;
}

/* rules for screens 768px and wider */
@media (min-width: 768px) {
    .flex-row {
        display: flex;
        flex-wrap: wrap;
    }

    .flex-column {
        flex-basis: 50%;
        padding-left: 0.25em;
        padding-right: 0.25em;    
    }
}

/* rules for screens 1200px and wider */
@media (min-width: 1200px) {
    .flex-column {
        flex-basis: 25%;
    }
}
```

In the `@media` rule for screens `768px` and wider, we added a new property to the `.flex-row`: `flex-wrap: wrap`. This tells the flexbox to wrap columns that don't fit on a single line down to the next line. Then in the `.flex-column` rule we added `flex-basis: 50%`, which tells the flexbox to make each column `50%` as wide as its parent element (the row). The `flex-basis` property is like `width`, except it applies only to flexbox columns, and will be ignored by browsers that don't support flexbox (IE). So on sceens `768px` to `1199px`, the columns will take up half of the row width, and the flexbox will wrap the third and fourth columns to a second line, thus creating a 2x2 layout.

We added another `@media` rule for screens `1200px` and wider, and in that we adjusted the `.flex-column` rule to reset the `flex-basis` to `25%`. This tells the flexbox to make each column a quarter of the overall row width, and since we have four columns, this will create a 1x4 layout with all columns on a single line.

To try this out, check out my [Responsive Flexbox CodePen](https://codepen.io/drstearns/pen/qNJAEG).

In all of these examples, we are setting the `flex-basis` of all columns to be the same, but you don't have to. For example, if you want a three column layout, but with the middle column consuming `80%` of the screen width and the outer columns taking up only `10%` each, just use different style class names on the different columns, and set the `flex-basis` property appropriately.

```css
* {
    box-sizing: border-box;
}

.flex-row {
    flex-grow: 1;
    display: flex;
}

/* styles for all columns */
.flex-column {
    padding-left: 0.25em;
    padding-right: 0.25em;
}

/* styles for the left/right side columns */
.flex-column-sides {
    flex-basis: 10%;
}

/* styles for center column */
.flex-column-center {
    flex-basis: 80%;
}
```

Remember that you can add multiple style classes to an HTML element (separated by spaces), so you can define one class for style properties that are common to all columns, and other style classes for properties that should apply to only one column (or set of similar columns). The HTML for the center column would then look like this:

```html
<div class="flex-column flex-column-center">
    ...
</div>
```

## Flexbox Nesting

Flexboxes can also be nested inside of each other to create very complex layouts. That is, the contents of a flexbox column can be another flexbox. This would allow you to create a layout where one of the columns contained columns of its own, and they could respond to the screen width using different rules than the containing flexbox.

Although flexbox nesting can be powerful, you should use it sparingly, as it tends to create layouts that are difficult for both users and developers to understand. 

## Responsive Container Elements

By default the `<body>` element will stretch to be as wide as the browser window (also called the "viewport"), and all block elements within the body will also stretch to be as wide as the body. This works well on narrower screens but it creates ridiculously wide layouts and long line-lengths on very wide desktop monitors, making your site difficult to read. It's common to have background images and colors stretch "wall-to-wall" but the main text content is often constrained in width so that it remains easy to read on all sizes of screens.

This is typically achieved by putting the main content of the page or a section in another element that is often called a "container." These container elements are just `<div>` elements with a style class name like `container`. We can then create a CSS rule that constrains the width of these elements.

For example, this is how the popular CSS Framework [Bootstrap](http://getbootstrap.com/css/#overview-container) defines their container class rules:

```css
.container {
    margin-left: auto;
    margin-right: auto;
    padding-left: 15px;
    padding-right: 15px;
}

@media (min-width: 768px) {
    .container {
        width: 750px;
    }
}

@media (min-width: 992px) {
    .container {
        width: 970px;
    }
}

@media (min-width: 1200px) {
    .container {
        width: 1170px;
    }
}
```

Setting `margin-left` and `margin-right` to `auto` will ensure that the container element remains horizontally centered when its width begins to be constrained, which happens on screens wider than `768px`. They increase the `width` of the element as the screen size increases, but only up to a point: the largest it will ever grow is `1170px` on screens wider than `1200px`.

To experience what this looks like, just resize your browser window. This site was built using Bootstrap, and the tutorial content is inside a `<div class="container">` element. You'll notice that the text will grow in size as you make the screen wider, but only up to a maximum of `1170px`. This keeps the line-lengths reasonable so that they are easy to read even when the browser window is very wide. But the header background image always stretches wall-to-wall.

## Keep Learning

This is just a taste of what media rules and flexbox can do. See Chris Coyier's [A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) for more details. Then play [Flexbox Froggy](http://flexboxfroggy.com/) to test your understanding of flexbox layout!


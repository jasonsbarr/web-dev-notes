# Basics of CSS: The Cascade, Selectors, Specificity, and Inheritance

## What is CSS?

CSS, which stands for Cascading Style Sheets, is the language used to define styles for web pages.

A CSS stylesheet is a collection of one or more style rulesets that tells your browser (or other rendering media, e.g. printing) how to display the document.

### Anatomy of a CSS ruleset

A ruleset has these parts:

- A selector
- A block, set apart with braces
- One or more rule definitions inside the block

For example:

```css
.some-class {
    display: inline-block;
    font-size: 2rem;
    font-family: serif;
}
```

This will style any elements with `class="some-class"` with the above rules.

The selector gives your CSS rules access to HTML elements so the browser knows how to display the affected markup.

## The basic cascade

The cascade is a set of rules that defines how your browser knows which CSS ruleset(s) to apply to which elements. It's based on where the ruleset appears relative to the document itself.

### Stylesheet origin

There are 3 different stylesheet origins that will apply in the vast majority of use cases. Other origins are either non-standard, deprecated, or obsolete (e.g. Override stylesheets, which were only ever implemented by the Gecko engine for non-CSS stylesheets).

Each stylesheet origin has its own level of _specificity_, which is how the browser knows which ruleset to apply. When there is a conflict between rulesets the more specific ruleset will be applied.

They are, in order of increasing specificity:

- User-agent, or browser default
- User-defined, which the user can use to override browser defaults
- Author-defined, which are the styles you write for your site or app

This means that, under default circumstances, the stylesheets you create will be used if there's a conflict between rulesets in stylesheets with different origins.

With stylesheets you create, there are 3 different locations for rulesets that are considered by the cascade.

#### External stylesheet

External stylesheets are the least-specific author-defined stylesheet. You'll link an external stylesheet to an HTML page using a `link` element:

```html
<link href="path/to/stylesheet.css" rel="stylesheet">
```

This is generally the preferred way to define style rulesets, because you can make changes in one place and they will propagate to all pages linked to the sheet.

#### Embedded `style` element

You can embed a `style` element in your document's `head`. This has greater specificity than an external stylesheet.

For example:

```html
<style>
    body {
        font-size: 62.5%;
        font-family: "Open Sans" sans-serif;
    }
</style>
```

Defining styles this way should generally be avoided, although sometimes it can be useful if you need some of your styles to load immediately instead of waiting for an external file to load.

#### Inline styles

Inline styles are the most specific location for author-defined styles. You define inline styles using the `style` attribute on an HTML element:

```html
<div style="margin: 20px auto; font-size: 3rem;">Content here</div>
```

Avoid inline styles whenever possible (and it's almost always possible). The high specificity of inline styles can and will cause significant headaches when trying to debug your CSS.

#### Understanding `!important`

There's one exception to the specificity rules related to stylesheet location, and that's `!important`.

Styles defined with `!important` **always** override other styles regardless of the specificity. 

`!important` style definitions are the _only_ way to override an inline style.

The only way to override an `!important` declaration is to write a ruleset with a more specific selector and then mark the property you're trying to set with `!important` as well.

If you find yourself having to use `!important` to get the results you need, you've very nearly always gone terribly wrong in designing your CSS.

## Selector types and specificity

Earlier I said CSS selectors give the stylesheet access to HTML elements so the browser can apply the appropriate rulesets.

Selectors define where the rulesets should be applied. There are several different kinds of selectors; here are some of the most important ones.

### Universal

The universal selector, `*`, selects every element at once.

It's generally a bad idea unless you know what you're doing, because it does indeed apply the block to every single element on the page unless you override it.

The most common place you'll find `*` is in a CSS reset, where the author is overriding browser default and user-defined styles to ensure more consistent styling across different browsers.

An overly simplified CSS reset might look something like:

```css
*::before, *::after, * { /* ::before and ::after are pseudo-elements */
    background-color: transparent;
    border: none;
    color: black;
    font-family: sans-serif;
    font-size: 16px;
    font-style: none;
    font-weight: normal;
    margin: 0;
    padding: 0;
    text-decoration: none;
}
```

**NOTE:** don't use the above as an _actual_ reset in your own work; resetting all styles to a default state is actually more complicated than you'd think. See [Eric Meyer's CSS Reset](https://meyerweb.com/eric/tools/css/reset/) for perhaps the most well-known example of a CSS reset that gets used on production sites.

`*` has no specificity, so any other selector will override it for the element(s) in question.

### Element selector

An element selector is simply the name of an HTML element. The ruleset for an element selector will be applied to all the selected elements on a page.

```css
a {
    /* sets default styles for all anchor elements
       on the page regardless of state (e.g. hover) */
    color: orangered;
    text-decoration: line-through;
}
```

The element selector has greater specificity than `*`, but less than all other types of selector.

### Class selector

The class selector selects all elements with a certain `class` attribute value.

```css
.className {
    display: inline-block;
    margin: 20px auto;
    text-align: center;
}
```

It has greater specificity than the element selector. You can also select an element that has multiple classes by chaining the selectors: `.class1.class2 {/* block of rule definitions */}`.

#### Pseudo-class selector

A pseudo-class is a status elements can have that's not specifically defined in the markup, like you would define a class, but still has a similar effect on styling to a class. Some examples of pseudo-classes include:

```css
element:hover
element:first-child
element:nth-of-type()
```

You can use pseudo-classes to make selections that would not otherwise be possible without adding an arbitrary class to the markup, which may not be possible in situations like having dynamically generated HTML from a server-side script.

```css
ul .list-item {
    color: blue;
}

ul :last-child {
    color: purple; /* overrides .list-item */
}
```

Pseudo-class selectors are more specific than class selectors.

#### Attribute selector

You can use an attribute selector to select all elements with a certain attribute, with or without a specified value.

```css
/* Select all a elements with any href attribute */
a[href] {
    text-decoration: underline;
}

/* select all a elements that link to Google.com */
a[href="google.com"] {
    font-weight: bold;
}
```

Attribute selectors have the same specificity as pseudo-class selectors.

### ID selector

The ID selector is the most specific single selector, and for that reason should be used with caution.

```css
#page-header {
    min-height: 40px;
    position: fixed;
    top: 0;
}
```

### Combining selectors and specificity math

You can also combine selectors. Doing so increases the specificity of the ruleset.

```css
/* More specific than any of these selectors alone */
#main-content ul .special-item {
    font-size: 1.6rem;
}
```

Sometimes you'll see tutorials and books that have you calculate the specificity of different selectors. For example you might see 0,0,0 where the first number counts ID selectors, the second is class selectors, and the third is element selectors. So:

```css
ul { /* 0,0,1 */
    /* rules */
}

ul li { /* 0,0,2 */
    /* rules */
}

/* The class selector overrides ANY NUMBER of element selectors */
.item { /* 0,1,0 */
    /* rules */
}

/* The ID selector makes any number of class selectors irrelevant */
#main .content-section { /* 1,1,0 */
    /* rules */
}
```

Note that _only_ the most specific selector matters in this calculation.

## Basic Inheritance

Child elements can inherit some properties from their parents. For example, if you define `font-size` on the `body` its value will be inherited as a default by every element that contains text on the page.

Not all properties can be inherited by default, nor would it be desirable if they could. If you had a `div` with a `border` set on it, you certainly wouldn't want all its children to inherit its border definition!

### The `inherit` value

You can explicitly set a child element to inherit the value for a particular property. This forces inheritance when it would not have otherwise been applied.

You would use this to override another rule.

In this example, setting `inherit` on `li:last-child` makes the text color of the last `li` element red instead of blue because a parent element has that `color` value.

```html
<style>
    .contentArea {
        color: red;
    }

    li.item {
        color: blue;
    }

    li:last-child {
        color: inherit;
    }
</style>

<body>
    <div class="contentArea">
        <ul>
            <li class="item">Hello</li>
            <li class="item">What's</li>
            <li class="item">Up</li>
        </ul>
    </div>
</body>
```

### The `initial` value

Setting a property's value to `initial` overrides any inherited values or values set by another style rule and sets it to the default value given by the [official CSS specification](<https://www.w3.org/TR/CSS/>).

**NOTE:** not supported by _any_ version of Internet Explorer or Opera Mini. It does work in Microsoft Edge, Opera, and Opera Mobile.

Style Guide
===========

General
-------

### Inheritence Vs Composition

In general prefer composition (and interface implementation) over inheritence except when the following is true:

1. The class and subclass exist in the same domain
2. The subclass can be considered to be a proper subtype of the superclass in all ways.
3. The functionality of the superclass is required in the subclass
4. The subclass has **additive** functionality to the subclass

CSS
---

### Layout

#### Indents
    
Four space indents, no tabs.

#### Line Width

In general, lines should not be longer than 100 columns wide. There will be exceptions to this rule for things like URLs.

#### Selector/Rule Anatomy

In general:

- The opening brace of a CSS rule should be on the same line as the selector.
- There should be a space after the colon in a rule but not before.
- All rules (including singles) should be suffixed with a semi-colon

CSS rules should be across multiple lines for efficient diffing. An exception would be a single line rule within a selector.

```css
.c-btn {
    display: inline-block;
    padding: 6px 12px;
}

.c-btn--filled { background: blue; }
```

In a multiple-selector ruleset, related selectors should be on the same line, unrelated ones on their own line.

```css
.foo, .foo--bar,
.baz {
    display: block;
    font-size: 14px;
}
```

#### Whitespace

- Separate closely related rulesets with 1 blank line
- Separate unrelated rulesets with 5 blank lines

```css
.foo { }

.foo--small {}




.bar { }

.bar--huge {}
```

#### Alignment

Try to align common strings in rulesets where possible.

```css
.foo {
    position: absolute;
    top:    0;
    left:   0;
    right:  0;
    bottom: 0;
    margin-left:   10px;
    margin-right:  10px;
    padding-left:  10px;
    padding-right: 10px;
}
```

### Reset

Use Normalize.css to ensure a universal baseline.

### ITCSS Structure

We followed a specificity-driven ordering of Sass files seperated into different 'layers'

The idea is that you `import` styles into your CSS in **specificity order**. This stops you having to fight against your own code when trying to apply a particular style. It also makes for leaner and more DRY CSS.

```
styles
├─ _settings
│   ├ _settings.colors.scss
│   └ _settings.dimensions.scss
├─ _tools
│   ├ _tools.functions.scss
│   └ _tools.mixins.scss
├─ _generic
│   ├ _generic.box-model.scss
│   └ _generic.normalize.scss
├─ _base
│   ├ _base.buttons.scss
│   ├ _base.headings.scss
│   └ _base.links.scss
├─ _objects
│   ├ _objects.lists.scss
│   └ _objects.media.scss
├─ _components
│   ├ _components.buttons.scss
│   ├ _components.header.scss
│   └ _components.logo.scss
├─ _trumps
│   └ _trumps.widths.scss
└ style.scss
```

These layers are then `import`ed into the main sheet, in the order above.

The layers are as follows:

#### Settings

The settings layer is just for global Sass variables. This includes things like colors and basic sizes across the site. Any settings that only affect a particular component should be included with that component.

The settings layer does not output any CSS.

#### Tools

The tools layer is primarily for mixins and functions. This includes things like shadow mixins and grid mixins.

The tools layer should not output any CSS.

#### Generic

The generic layer is primarily for third party code which will not interact with our code base directly. This includes things that will always be overriden by our code (Bootstrap, Normaliza, Owl etc).

#### Base

The base layer should define what base elements look like with no classes applied. This should be used to remove browser-based "default" styles that aren't inline with the style guide for the project. This includes things like borders on form controls and removing margins.

#### Objects

The objects layer is for structural components with no styling associated. This includes things like the famous [Media Object](http://www.stubbornella.org/content/2010/06/25/the-media-object-saves-hundreds-of-lines-of-code/), Island Object or Flag Object.

#### Components

This is where the majority of your CSS will be. This is where full section of UI are styled. An example might be a carousel or an accordian.

#### Trumps

The trumps layer is for anything that could be marked as !important. The rules with maximum specificity. Examples include floats, text alignment, background colours.

### BEM Naming 

We use a Namespace-Block__Element--Modifer naming convention. This allows classes to be easily grouped and for the developer to get semantic meaning from the class names.

For example:

```sass
.c-btn--big                     //A big button in the component layer.
.o-media__image                 //An image in a media object in the object layer.
.o-flag--more-padding__content  //A content div inside a flag object with more padding (an extreme example).
```
```html
<a class="c-btn c-btn--big">
    <span class="c-btn__chevron">Let's Go</span>
</a>
```

The hierarchy of your classes does not need to match the DOM. It's purely for semantic benefit for the developer. If the class looks absurdly long, then it is.

#### Benefits

The advantage of this naming convention is that we can see the relationship between classes at a glance without knowing about the CSS in detail.

```html
<div class="media person premium">
    <img class="image" />
    <p class="body name">Steve</p>
</div>
```

How are all these classes related? Where are the dependencies?

```html
<div class="o-media  c-person c-person--premium">
    <img class="o-media__image" />
    <p class="o-media__body  c-person__name">Steve</p>
</div>
```

Now we can see, at a glance (also notice the two spaces between unrelated classes), how this component is built up and where to find the Sass code that makes it happen.  

### Only select on classes

Only use classes for styling selectors and never use IDs or element types (except in the base layer). This ensures a nice, even specificity increase as the stylesheet cascades down.

### Naming Things

When naming UI elements, favour generic, even abstract names. Instead of #dashboard, go with .widget-group. Instead of .advert-box go with .o-island. This encourages you to think about how your selectors can be reused and modularized in a more effective way.

### Sass

Sass should be used on all but the most simple of projects where the setup time would be a significant portion of the total project time.

#### Nesting

Nesting in Sass should be avoided where possible to avoid unnecessary specificity boosts. It is better to define relationships with namespacing than with parent selectors.

#### Autoprefixer

Autoprefixer should be used to account for different browser prefixes and raw browser prefixes should never be in human-readable source.

#### Extend

The Sass feature '@extend' should be used rarely as it messes with the source order of the CSS which will affect the specificity of your CSS in a way that is out of your control. Instead, favour the composition of multiple classes rather than monolithic extends.

NB: Although extend does, potentially, reduce the amount of CSS in terms of bytes, the nature of the repeated code is crushed by gzip anyway. Once the CSS is gzipped, the non-extend version is usually smaller due to many repeated strings.

#### Units

Use pixel units for sizing elements.

HTML
----

### Data Attributes

Data attributes should be used to attach arbitrary data to html elements.

#### Booleans

Booleans in data attributes should be represented as ```true``` or ```false```. JQuery automatically recognises these values and converts them to true boolean primitives.

In VB.NET, Razor views will automatically output ```True``` and ```False``` when used inline. These should be converted to lowercase with a helper function.

Javascript
----------

### Structure/Layout

#### Indentation

Four spaces, no tabs.

#### Braces

#### Comments

### Variable Declarations

Variable declarations should be made on separate lines with individual var statements for effective diffing.

```javascript
// Good
var foo;
var bar = 1;
var baz;

// BAD

var foo, bar, baz;

// ALSO BAD

var foo,
    bar,
    baz;
```

### Naming Things

#### Variables

- Variables in the public scope should be PascalCased
- Private variables should be camelCased;
- Constants should be UPPER_CASED;

```javascript
(function() {
    
    var HALF_PI = 1.57;

    var myVar = "Cakes and pies";

    var myFunc = function piMult(input) {
        return input * HALF_PI * 2;
    };

    window.MyModule = {
        piMult = piMult
    }

})();
```

#### Functions

Always name functions. This especially makes debugging easier because the console will spit out the function name in which an exception was thrown.

```javascript
arr.each(function plusOne(item) {
 item.x ++;
});
```

### Minification/Concatenation

All JS should be minfied and concatenated to a single file for production. Libraries should be sourced from a CDN if possible or concatenated into 

### DOM Selectors

Only select DOM elements using special classes and do not mix classes for styling and classes for DOM selection. Classes for DOM selection should be prefixed with js-

```html
<a href="#" class="c-btn c-btn--big  js-signup-button">Sign Up</a>
```

DOM selector classes do not follow the NBEM naming structure.

Don't use `data-` attributes for selection. They should only be used for arbitrary data storage.

### Closures

### Equality

When checking for equality === should be used.

```javascript
if (x === 'lol') {}
```

When checking for an undefined variable typeof should be used.

```javascript
if (typeof x !== 'undefined') {}
```

### Strict Mode

Modern JS has deprecated non-strict mode so strict mode should always be used

```javascript
(function() {
    'use strict';
})();
```

### Quotes

Single quotes (') should be used.

```javascript
var foo = 'lol';
```

C#
--

Generally, the C# style should adhere to the current Microsoft Style Guide.

### Structure/Layout

Refer to the Javascript layout section for anything missing here

### Naming Things

#### Classes/Interfaces

#### Methods

#### Fields

#### Properties

### Comments

### String Interpolation/Concatenation

### Implicit Variable Typing

### Exception Handling

### LINQ Queries

### IOC/Dependency Injection

Visual Basic .NET
-----------------

**Just don't**

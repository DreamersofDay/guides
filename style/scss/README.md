SCSS
======

* [CapitalizationInSelector](#capitalizationinselector)
* [ColorKeyword](#colorkeyword)
* [DeclarationOrder](#declarationorder)
* [EmptyLineBetweenBlocks](#emptylinebetweenblocks)
* [IdWithExtraneousSelector](#idwithextraneousselector)
* [Indentation](#indentation)
* [NameFormat](#nameformat)
* [PlaceholderInExtend](#placeholderinextend)
* [SelectorDepth](#selectordepth)
* [Shorthand](#shorthand)
* [SpaceAfterComma](#spaceaftercomma)
* [SpaceAfterPropertyColon](#spaceafterpropertycolon)
* [SpaceAfterPropertyName](#spaceafterpropertyname)
* [SpaceBeforeBrace](#spacebeforebrace)
* [VendorPrefixes](#vendorprefixes)

## CapitalizationInSelector

IDs, classes, types, placeholders, and pseudo-selectors should be all lowercase.

**Bad: capitalized class name**
```scss
.Button {
  ...
}
```

**Good: all lowercase**
```scss
.button {
  ...
}
```

## ColorKeyword

Prefer hexadecimal color codes over color keywords.

**Bad: color keyword**
```scss
color: green;
```

**Good: hexadecimal color**
```scss
color: #0f0;
```

Defining colors directly in properties is usually a smell. When you color your
body text in a number of places, if you ever want to change the color of the
text you'll have to update the explicitly defined color in a number of places,
and finding all those places can be difficult if you use the same color for
other elements (i.e. a simple find/replace may not always work).

A better approach is to use global variables like `$color-text-body` and refer
to this variable everywhere you want to use it. This makes it easy to update
the color, as you only need change it in one place. It is also more
intention-revealing, as seeing the name `$color-text-body` is more descriptive
than `#333` or `black`. Using color keywords can obfuscate this, as they look
like variables

## DeclarationOrder

Write `@extend` statements first in rule sets, followed by property
declarations and then other nested rule sets.

**Bad: `@extend` not first**
```scss
.fatal-error {
  color: #f00;
  @extend %error;

  p {
    ...
  }
}
```

**Good: `@extend` appears first**
```scss
.fatal-error {
  @extend %error;
  color: #f00;

  p {
    ...
  }
}
```

The `@extend` statement functionally acts like an inheritance mechanism, which
means the properties defined by the placeholder being extended are rendered
before the rest of the properties in the rule set.

Thus, declaring the `@extend` at the top of the rule set reminds the developer
of this behavior.

## EmptyLineBetweenBlocks

Separate rule, function, and mixin declarations with empty lines.

**Bad: no lines separating blocks**
```scss
p {
  margin: 0;
  em {
    ...
  }
}
a {
  ...
}
```

**Good: lines separating blocks**
```scss
p {
  margin: 0;

  em {
    ...
  }
}

a {
  ...
}
```

## IdWithExtraneousSelector

Don't combine additional selectors with an ID selector.

**Bad: `.button` class is unnecessary**
```scss
#submit-button.button {
  ...
}
```

**Good: standalone ID selector**
```scss
#submit-button {
  ...
}
```

While the CSS specification allows for multiple elements with the same ID to
appear in a single document, in practice this is a smell.  When
reasoning about IDs (including selector specificity), it should suffice to
style an element with a particular ID based solely on the ID.

Another possible pattern is to modify the style of an element with a given
ID based on the class it has. This is also a smell, as the purpose of a CSS
class is to be reusable and composable, and thus redefining it for a specific
ID is a violation of those principles.

Even better would be to
[never use IDs](http://screwlewse.com/2010/07/dont-use-id-selectors-in-css/)
in the first place.

## Indentation

Use two spaces per indentation level. No hard tabs.

**Bad: four spaces**
```scss
p {
    color: #f00;
}
```

**Good: two spaces**
```scss
p {
  color: #f00;
}
```

## NameFormat

Functions, mixins, and variables should be declared with all lowercase letters
and hyphens instead of underscores.

**Bad: uppercase characters**
```scss
$myVar: 10px;

@mixin myMixin() {
  ...
}
```

**Good: all lowercase with hyphens**
```scss
$my-var: 10px;

@mixin my-mixin() {
  ...
}
```

Using lowercase with hyphens in CSS has become the _de facto_ standard, and
brings with it a couple of benefits. First of all, hyphens are easier to type
than underscores, due to the additional `Shift` key required for underscores on
most popular keyboard layouts. Furthermore, using hyphens in class names in
particular allows you to take advantage of the
[`|=` attribute selector](http://www.w3.org/TR/CSS21/selector.html#attribute-selectors),
which allows you to write a selector like `[class|="inactive"]` to match both
`inactive-user` and `inactive-button` classes.

The Sass parser automatically treats underscores and hyphens the same, so even
if you're using a library that declares a function with an underscore, you can
refer to it using the hyphenated form instead.

## PlaceholderInExtend

Always use placeholder selectors in `@extend`.

**Bad: extending a class**
```scss
.fatal {
  @extend .error;
}
```

**Good: extending a placeholder**
```scss
.fatal {
  @extend %error;
}
```

Using a class selector with the `@extend` statement statement usually results
in more generated CSS than when using a placeholder selector.  Furthermore,
Sass specifically introduced placeholder selectors in order to be used with
`@extend`.

See [Mastering Sass extends and placeholders](http://8gramgorilla.com/mastering-sass-extends-and-placeholders/).

## SelectorDepth

Don't write selectors with a depth of applicability greater than 3.

**Bad: selectors with depths of 4**
```scss
.one .two .three > .four {
  ...
}

.one .two {
  .three > .four {
    ...
  }
}
```

**Good**
```scss
.one .two .three {
  ...
}

.one .two {
  .three {
    ...
  }
}
```

Selectors with a large [depth of applicability](http://smacss.com/book/applicability)
lead to CSS tightly-coupled to your HTML structure, making it brittle to change.

Deep selectors also come with a performance penalty, which can affect rendering
times, especially on mobile devices. While the default limit is 3, ideally it
is better to use less than 3 whenever possible.

## Shorthand

Prefer the shortest shorthand form possible for properties that support it.

**Bad: all 4 sides specified with same value**
```scss
margin: 1px 1px 1px 1px;
```

**Good: equivalent to specifying 1px for all sides**
```scss
margin: 1px;
```

## SpaceAfterComma

Commas in lists should be followed by a space.

**Bad: no space after commas**
```scss
@include box-shadow(0 2px 2px rgba(0,0,0,.2));
color: rgba(0,0,0,.1);
```

**Good: commas followed by a space**
```scss
@include box-shadow(0 2px 2px rgba(0, 0, 0, .2));
color: rgba(0, 0, 0, .1);
```

## SpaceAfterPropertyColon

Properties should be formatted with a single space separating the colon from
the property's value.

**Bad: no space after colon**
```scss
margin:0;
```

**Bad: more than one space after colon**
```scss
margin:  0;
```

**Good**
```scss
margin: 0;
```

## SpaceAfterPropertyName

Properties should be formatted with no space between the name and the colon.

**Bad: space before colon**
```scss
margin : 0;
```

**Good**
```scss
margin: 0;
```

## SpaceBeforeBrace

Opening braces should be preceded by a single space.

**Bad: no space before brace**
```scss
p{
  ...
}
```

**Bad: more than one space before brace**
```scss
p  {
  ...
}
```

**Good**
```scss
p {
  ...
}
```

## VendorPrefixes

Use Bourbon instead of specifying vendor prefixes

**Bad**
```scss
p {
  background: -moz-linear-gradient(top, #e3e3e3 0%, #dddddd 100%)
  background: -webkit-gradient(top, #e3e3e3 0%, #dddddd 100%)
  background: -webkit-linear-gradient(top, #e3e3e3 0%, #dddddd 100%)
  background: -o-linear-gradient(top, #e3e3e3 0%, #dddddd 100%)
  background: -ms-linear-gradient(top, #e3e3e3 0%, #dddddd 100%)
  background: linear-gradient(top, #e3e3e3 0%, #dddddd 100%)
}
```

**Good**
```scss
p {
  @include linear-gradient(top, #e3e3e3 0%, #dddddd 100%)
}
```

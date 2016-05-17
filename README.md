# PostCSS Nested ancestors [![Build Status][ci-img]][ci]

[PostCSS] plugin to reference any ancestor selector in nested CSS.

[PostCSS]: https://github.com/postcss/postcss
[ci-img]:  https://travis-ci.org/toomuchdesign/postcss-nested-ancestors.svg
[ci]:      https://travis-ci.org/toomuchdesign/postcss-nested-ancestors

## Installation

```console
$ npm install postcss-nested-ancestors
```

## Usage
When writing modular nested CSS, `&` current parent selector is often not enough.

**PostCSS Nested ancestors** introduce `^&` selector which let you reference **any parent ancestor selector** with an easy and customizable interface.

This plugin should be used **before** a POSTCSS rules unwrapper like [postcss-nested](https://github.com/postcss/postcss-nested).

See [PostCSS] docs for examples for your environment.

### Ancestor selector schema

```
    .level-1 {
|   |   .level-2 {
|   |   |   .level-3 {
|   |   |   |   .level-4 {
|   |   |   |   |
|   |   |   |   --- & {}        /*      & = ".level-1 .level-2 .level-3 .level-4" */
|   |   |   ------- ^& {}       /*     ^& = ".level-1 .level-2 .level-3"          */
|   |   ----------- ^^& {}      /*    ^^& = ".level-1 .level-2"                   */
|   --------------- ^^^& {}     /*   ^^^& = ".level-1"                            */
------------------- ^^^^& {}    /*  ^^^^& = ""                                    */
                }
            }
        }
    }
```

### A real example

```css
/* Without postcss-nested-ancestors */
.MyComponent
    &-part{}
    &:hover {
        > .MyComponent-part {} /* Must manually repeat ".MyComponent" for each child */
    }
}

/* With postcss-nested-ancestors */
.MyComponent
    &-part{}
    &:hover {
        > ^&-part {} /* Skip ":hover" inheritance here */
    }
}

/* After postcss-nested-ancestors */
.MyComponent {
    &-part{}
    &:hover {
        > .MyComponent-part {}
}

/* After postcss-nested */
.MyComponent {}
.MyComponent-part {}
.MyComponent:hover {}
.MyComponent:hover > .MyComponent-part {} /* No ":hover" inheritance here! */

```

## Why?
Currently another plugin - [postcss-current-selector] - has tried to solve the problem of referencing ancestors selector. It works great, but its approach involves assigning ancestor selectors to special variables to be later processed by a further postcss plugin [postcss-simple-vars].

[postcss-nested-ancestors] instead replaces special ancestor selectors, makes no use of variable assignment and produces an output ready to be unwrapped with [postcss-nested].

## Options

### placeholder

Type: `string`
Default: `^&`

Ancestor selector pattern (utility option to automatically set both `levelSymbol` and `parentSymbol`)

### levelSymbol

Type: `string`
Default: `^`

Define ancestor selector fragment reative to the matching nesting level

### parentSymbol

Type: `string`
Default: `&`

Ancestor selector base symbol

# Gridtacular v2.0

Gridtacular is a modular flexbox powered grid system designed to fit within your current workflow.

# Getting started

To install Gridtacular you can either clone the source and insert into your current workflow or alternatively you can install using either NPM or Yarn

### NPM installation

```
npm install gridtacular
```

### Yarn installation

```
yarn add gridtacular
```

# Setup

Gridtacular is very modular and can be configured with a series of nested arguments. 

First you need to include the grid system in your workflow by adding the following to your main scss file. 
*Make sure the path is correct for your environment.*

```scss
@import 'node_modules/_gridtacular';
```

At this stage Gridtacular will not add any code to your stylesheet whatsoever becausde you must configure the settings first.

## Basic Setup Example

To begin lets setup a very simple 12 column grid with a 24px gutter.

```scss
$grid_args:(
    config: (
        gridclass: 'g',
        itemclass: 'gi--'
    ),
    grids: (
        mygrid: (
            columns: 12,
            suffix: null,
            gutter: 24px
        )
    )
);

@include grid_generate($grid_args);
```

Now if you compile your Scss you will notice that there are now a bunch of new classes and you have a basic working Grid System.

### So what is happening here?

Gridtacular grids consist of a grid container which is filled with grid items. Traditionally grid s ystem in the past may have called the container a 'row' and the grid items a 'column'.

We have create a Sass map and assigned it to a variable called `$grid_args` which we pass through to the `grid_generate()` function.
There are two sets of arguments within `$grid_args`, `config` and `grids`. 

`config` is itself an array of arguments that control the overall configuration of how the grid systemn works. In the above example we have defined two rules. 

The first argument `gridclass` is the css class base for the Grid container. Above we have used `g` as the basis for our grid class name which means when we want to add a grid to our frontend we can write something like:

```html
<div class="g"></div>
```

The second argument `itemclass` is the css class base for the grid items. and can be written like so:

```html
<div class="gi--1-3"></div>
```

You will notice that there is some extra text added to the class here that is because `gi--` is our class _prefix_ and prevents code bloat. The extra text `gi--1-3` translates into `.gi--[no of items to span]-[total items]` so in our example our grid item will take up 1/3rd of the total grid width.

If you wanted to create a layout that has two columns, one that is 2/3rds wide for content and one that is 1/3rds wide as a sidebar the markup would look like this:

```html
<div class="g">
    <div class="gi--2-3">
        <!-- Add content here -->
    </div>
    <div class="gi--1-3">
        <!-- Add sidebar here -->
    </div>
</div>
```

Gridtacular has a special feature called `equivalent-fractions` which creates additional selectors for simplified fractions. this sounds complicated but it is here to make your code more readable and understandable. Above we created a 12 column grid, 12 columns has become somewhat of a standard for web based grids largely because it can be divided by 6,4,3,2 and 1. So we could write all our classes as 12ths fractions e.g. 

```html
<div class="gi--4-12"></div>
``` 

Alternatively we can write the same thing as a simplified _equivalent fraction_ e.g. 
```html
<div class="gi--1-3"></div>
```

To make sure that we keep the css output as lean as possible we define these in a single rule which looks something like so in the compiled css:

```css
.gi--1-3, .gi--2-6, .gi--4-12 {
    flex-basis: 33.33333%;
    max-width: 33.33333%;
}
```

# Responsive grids

The benefits of Gridtacular become apparent as soon as you need a responsive grid. Most frontend developers will define a number of _breakpoints_ across their documents using media queries that allow css styles to be executed only when the browser width is between two specified points.

Gridtacular allows us to define grids on a per breakpoint basis. Each additional grid is governed by its own set of rules at each breakpoint.

## Responsive Grid Setup Example

Going back to our config arguments we setup earlier lets say we want to add a breakpoint for screens that are larger than 800px wide (*the grid accepts most major css units including ems and rems)

```scss
$grid_args:(
    config: (
        gridclass: 'g',
        itemclass: 'gi--'
    ),
    grids: (
        small: (
            columns: 12,
            suffix: null,
            gutter: 24px
        ),
        medium: (
            columns: 12,
            suffix: '--m',
            breakpoint_min: 800px,
            breakpoint_max: null,
            gutter: 36px
        ),
    )
);

@include grid_generate($grid_args);
```

By running this code we create two unique grids. The first `small` grid will apply at any screensize **greater than 0**. Our `medium` grid will only apply to browser widths **greater than 800px**.

At first this seems counterintuitive and you may think that it is redundant to have a conflict like this as the small grid will still apply even when the browser is over 800px wide.

This is by design and allows you to be extremely efficient with your markup.

When creating new responsive grids there are three additional argument that are required: `suffix`,`breakpoint_min` and `breakpoint_max`.

`suffix` is output after our items/total-items rules the suffix is added to allow us to target different browser widths. e.g.

```html
<div class="gi--1-3--m"></div>
```

`breakpoint_min` is the minimum width of the browser that the rule will begin working.
`breakpoint_max` is the maximum width of the browser that the rule will apply to.

In our example above we have a `breakpoint_min` of `800px` and the `breakpoint_max` is set to `null`. By setting the value to null it means that the grid will apply to **All** browser widths larger than the `breakpoint_min` value. You can define a maximum size if you need to but I find that leaving it to null makes it much easier in situations where you have the same grid items setup across multiple grid sizes.

## So why is all this useful?

Lets take the grid we setup above:

```html
<div class="g">
    <div class="gi--2-3">
        <!-- Add content here -->
    </div>
    <div class="gi--1-3">
        <!-- Add sidebar here -->
    </div>
</div>
```

Now lets say that the columns appear too small and are causing issues when viewed on small screens such as smartphones. What we would like to do is have the columns fill the entire grid when viewed on a small screen with the second grid item appearing below the first item but we also want to maintain the 2/3rds + 1/3rd grid item settings when the browser is bigger than 800px wide:

```html
<div class="g">
    <div class="gi--1-1  gi--2-3--m">
        <!-- Add content here -->
    </div>
    <div class="gi--1-1  gi--1-3--m">
        <!-- Add sidebar here -->
    </div>
</div>
```

# Reference

## Grid Setup Reference

Option | Type | Default | Description
------ | ---- | ------- | -----------
config | array | n/a | configuration options for the grid system
grids | array | n/a | array of grid settings

## Options Reference

Option | Type | Default | Description
------ | ---- | ------- | -----------
gridclass | string | .g | The base class for the Grid Object
itemclass | string | .gi-- | The base class for Grid Items (-- is used as a seperator but not mandatory)
push | bool | false | Enable/disable push classes
pushclass | string | .p-- | The base class for pushing items
debug | bool | false | If set to true visual debugging is enabled


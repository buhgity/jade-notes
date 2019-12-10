# Extends
## Template Inheritance
- The `extends` keyword allows a template to extend a layout or parent template.
- Can override certain pre-defined blocks of content.
```jade
//- layout.jade
doctype html
html
  head
    block title
      title Default title
  body
    block content

//- index.jade
extends ./layout.jade

block title
  title Article Title

block content
  h1 My Article
```

# Filters
Filters let you use other languages within a jade template.
- Take a block of plain text as an input.
- All JSTransformers can be used as jade filters.
Popular filters include:
 :coffee-script,
 :babel,
 :uglify-js,
 :less, and
 :markdown-it.
```html
:markdown
  # Markdown
  I often like including markdown documents.
script
  :coffee-script
    console.log 'This is coffee script'
<!-- Output -->
<h1>Markdown</h1>
<p>I often like including markdown documents.</p>
<script>console.log('This is coffee script')</script>
```

### Warning
*Filters are compile time. This makes them fast but means they cannot support dynamic content.
Built in filters are not available in the browser as they would not all work. Providing you compile your templates server side, they will still work fine though.*

# Includes
- Allow to insert the contents of one jade file into another.
- Including files that are not jade just includes the raw text.
- Can combine filters with includes to filter things as you include them.
```jade
//- index.jade
doctype html
html
  head
    title An Article
  body
    include:markdown article.md
```

# Inheritance
- Supports template inheritance via the block and extends keywords.
- A block is simply a “block” of Jade that may be replaced within a child template.
- This process is recursive.

Block append / prepend
======================
Jade allows to replace (default), prepend, or append blocks.
Example:
```jade
// layout.jade
html
  head
    block head
      script(src='/vendor/jquery.js')
      script(src='/vendor/caustic.js')
  body
    block content

extends layout

// index.jade
block append head
  script(src='/vendor/three.js')
  script(src='/game.js')
```
*When using block append or block prepend the block is optional:*

Interpolation
=============
## String Interpolation, Escaped
- the code in between #{ and } is evaluated, escaped,
- and the result buffered into the output of the template being rendered.
- This can be any valid JavaScript expression.

## String Interpolation, Unescaped ```!{riskyBusiness}```

## Danger
*Keep in mind that buffering unescaped content into your templates can be mighty risky if that content comes fresh from your users. Never trust user input!*

## Tag Interpolation
```#[a(href='https://google.com')]```

Iteration
=========
Two primary methods of iteration, each and while.

## each
Iterate over arrays and objects within a template:
```jade
// iterating array
ul
  each val, index in [1, 2, 3, 4, 5]
    li= val
// iterating object
ul
  each val, index in {1:'one',2:'two',3:'three'}
    li= index + ': ' + val
```
The object or array to iterate over is just plain JavaScript so it can be a variable or the result of a function call or almost anything else. e.g.
```jade
- var values = [];
ul
  each val in values.length ? values : ['There are no values']
    li= val
```
*can also use `for` as an alias of each.*

## while
Can also use while to create a loop:
```jade
- var n = 0
ul
  while n < 4
    li= n++
```

Mixins
======
Mixins allow you to create reusable blocks of jade.
```jade
//- Declaration
mixin list
  ul
    li foo
    li bar
    li baz

//- Use
+list
+list
```

## Mixins Function
Compiled to functions and can take arguments:
```jade
mixin pet(name)
  li.pet= name
ul

+pet('cat')
+pet('dog')
+pet('pig')
```

## Mixins Blocks
Can also take a block of jade to act as the content.
```jade
// Declaration
mixin article(title)
  .article
    .article-wrapper
      h1= title
  if block
    block
  else
    p No content provided

+article('Hello world')

+article('Hello world')
// take below block as block parameter of mixins
p This is my
p Amazing article
```

## Mixin Attributes
- Mixins also get an implicit attributes argument taken from the attributes passed to the mixin.
```jade
mixin link(href, name)
  //- attributes == {class: "btn"}
  a(class!=attributes.class, href=href)= name

+link('/foo', 'foo')(class="btn")
```

## Note
*The values in attributes are already escaped so you should use != to avoid escaping them a second time.*

- You can also use with `&attributes`
```jade
mixin link(href, name)
  a(href=href)&attributes(attributes)= name

+link('/foo', 'foo')(class="btn")
// output HTML
<a href="/foo" class="btn">foo</a>
```

## Rest Arguments
Take an unknown number of arguments using the “rest arguments” syntax.
```jade
mixin list(id, ...items)
  ul(id=id)
    each item in items
      li= item

+list('my-list', 1, 2, 3, 4)
```

Plain Text
==========
Three common ways.

## Piped Text
Prefix the line with a | character (pronounced “pipe”).
```jade
| Plain text can include <strong>html</strong>
p
| It must always be on its own line
// Output HTML
Plain text can include <strong>html</strong>
<p>It must always be on its own line</p>
```

## Inline in a Tag
Put text in a tag just by adding it inline after a space.
```jade
p Plain text can include <strong>HTML</strong>
```

## Block in a Tab
Add a `.` after the tag (with no preceding space)
```jade
script.
if (usingJade)
console.log('you are awesome')
else
console.log('use jade')
```

Tags
====
Text at the start of a line (or after only white space) represents an HTML tag. Indented tags are nested.

## Block Expansion
To save space, jade provides an inline syntax for nested tags.
```jade
a: img

<a><img/></a>
```

## Self Closing Tags
When using the xml doctype, you can also explicitly self close a tag by simply appending the / character.
*Only do this if you know what you’re doing*
```jade
foo/
foo(bar='baz')/
<foo/>
<foo bar="baz"/>
```

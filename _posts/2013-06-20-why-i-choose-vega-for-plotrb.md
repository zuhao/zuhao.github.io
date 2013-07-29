---
layout: post
title: "Why I Choose Vega for Plotrb"
category: gsoc2013
---

It is a very intriguing project to develop a plotting library in Ruby based on D3. The reason is that till now I still don't think there's a perfect solution to solve every problem raised. In this post, I will try to explain the rationale of forgoing the original plan of developing a D3 wrapper, and choosing [Vega](https://github.com/trifacta/vega) as a "glue" layer instead.

### The original plan

Originally, I was planning to develop a "low-level" wrapper, in the sense that the syntax would be as close to D3's as possible. D3 is so powerful that you can fine-tune every tiniest detail of your visualization, and thus enable you to produce remarkably complex and professional data-driven documents.

This makes a wrapper difficult to deviate from the original D3 syntax, because by doing so, I have to impose a great amount of personal understanding of how a visualization should be done, and what level of details should be exposed to the users. Such a opinionated DSL would seem to defeat the whole purpose of D3's customizability.

Hence, I came up with a [demo](https://github.com/zuhao/d3rb) of the wrapper. As an example, the following Ruby code generates a block of javascript, which in turn renders ten circles with random radii and positions in the browser.

Ruby:

```ruby
width, height = 800, 400
range = D3.new.range(10)
svg = D3.new.select('body').append('svg').attr('width', width).attr('height', height)
d = svg.selectAll('circle') do
  data(range).enter
  append 'circle' do
    attr 'cx', "function() {return Math.random() * #{width};}"
    attr 'cy', "function() {return Math.random() * #{height};}"
    attr 'r', "function() {return 50 + Math.random() * 50;}"
    style 'fill-opacity', 0.1
    style 'stroke', '#000'
  end
end
```

Javascript:

```javascript
<script src='http://d3js.org/d3.v3.min.js'></script>
<script type='text/javascript'>
d3.select('body').append('svg').attr('width',800).attr('height',400).selectAll('circle')
.data(d3.range(10)).enter().append('circle')
.attr('cx',function() {return Math.random() * 800;})
.attr('cy',function() {return Math.random() * 400;})
.attr('r',function() {return 50 + Math.random() * 50;})
.style('fill-opacity',0.1).style('stroke','#000')
</script>
```

Generated SVG in browser:
![circles](https://raw.github.com/zuhao/d3rb/master/examples/images/10-random-circles.png)

As you can see, it works, but the syntaxes are extremely alike.

### What's wrong with it

Something subtle, but nevertheless important, is completely missing. Yes, I am talking about the *elegance* of Ruby. To put it bluntly, it just looks ugly for a Ruby DSL. Although the aesthetic point of view is personal, there is one thing which we can consider the biggest flaw in this approach.

We just can't get rid of the javascript!

First of all, let's jump outside of the box, and take a look at the problem again. The following are just a few *facts* that we can't change.

1. We want to write the code in Ruby. (*duh...*)
2. Javascript has to be there in the end because
    * We don't want to re-implement all D3 functionality in Ruby
    * We want the visualization generated in the browser, and possibly with interactivity.
    * The customizability of D3 means we often have to embed our own javascript functions for many tasks such as data manipulation.
3. There isn't a mature solution to compile arbitrary Ruby method into javascript functions.

Regarding the last point, the closest thing we may have is perhaps [Opal](http://opalrb.org/), which is a promising project designed specifically to do the job, but here's what we've got.

A simple Ruby function that can be applied to manipulate data,

```ruby
my_func = lambda { |x| (x * Kernel::rand).ceil }
```

is then compiled into the following javascript code:

```javascript
(function(__opal) {
  var TMP_1, $a, $b, self = __opal.top, __scope = __opal, $mm = __opal.mm, $nil = __opal.nil, __breaker = __opal.breaker, __slice = __opal.slice, my_func;
  return my_func = ($a = ((($b = self) == null ? $b = $nil : $b).$lambda || $mm('lambda')), $a._p = (TMP_1 = function(x) {

    var self = (TMP_1.hasOwnProperty('_s') ? TMP_1._s : this), $a, $b, $c, $d, $e;

    return ((($a = ($b = x, $c = ((($d = (($e = __scope.Kernel) === undefined ? __opal.cm("Kernel") : $e)) == null ? $d = $nil : $d).$rand || $mm('rand')).call($d), typeof($b) === 'number' ? $b * $c : $b['$*']($c))) == null ? $a = $nil : $a).$ceil || $mm('ceil')).call($a)
  }, TMP_1._s = self, TMP_1), $a).call($b)
})(Opal);
```

I'm not sure about you, but this is certainly unacceptable to me.

A workaround would be to embed custom javascript functions (for example as a string) inside the Ruby code. It's cleaner than the above but again, it doesn't make much sense. If the DSL syntax is almost same as D3's, and custom functions have to be written in javascript directly, why not save all the hassle and develop in plain ol' javascript?

### How does Vega solve the problem

Vega does it by entirely avoiding the problem of compiling some interpreted servers-side dynamic typing language such as Ruby, into client-side Javascript. If it's extremely difficult to translate one language into another, why not find something else they're both comfortable with?

No more translation, Vega itself is written natively in javascript. More importantly, the specification, which is all Vega needs to "describe" visualization, is in JSON format. It's much easier for Ruby to work with Hashes and generate JSON objects for Vega to process, than to translate the code directly into javascript.

Another advantage of Vega is that it's a "declarative" grammar. Comparing to D3, it is higher level in terms of abstraction and simplicity for the users. For example, if you want to plot a bar chart in D3, the axes alone would require much of your attention, and "bother" you with all kinds of details such as scales, orientations, tick sizes, padding, etc. But more often than not, you just want a damn pair of axes! Vega takes care of many of the details, and let you focus on more important things. To declare axes in the specification, it's as simple as the following four lines.

```json
"axes": [
    {"type": "x", "scale": "x"},
    {"type": "y", "scale": "y"}
]
```

### Trade-offs?

We don't live in a perfect world; the sooner we realize it, the better off we will be. At the end of the day, it's all about trade-offs. JSON, as an object notation by its nature, of course can't match the expressiveness of full-blown languages like javascript. We do have to face trade-offs between simplicity and customizability. For example, many of the interactive features are not yet supported by Vega, due to the limitation of JSON. Nevertheless, we can improve on this and perhaps add support for some "instructions" for interactivity in the future.

However, the reason that simplicity eventually wins the argument is that for many people, especially those in the science community, plotting should be something they can quickly get done with. They probably shouldn't spend so much time on every tiny detail of the visualization like a graphic designer does. If you find yourself in this category, I'm pretty sure this tool will be the one for you.

On the other hand, as [suggested](https://github.com/trifacta/vega/wiki/Vega-and-D3) by the author of Vega, if you need very complex or novel visualizations, it's definitely worth your time to learn and master D3.
# Charty - Visualizing your data in Ruby

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/red-data-tools/charty/masater?filepath=iris_dataset.ipynb)

Charty is open-source Ruby library for visualizing your data in a simple way.
In Charty, you need to write very few lines of code for representing what you want to do.
It lets you focus on your analysis of data, instead of plotting.

![](https://github.com/red-data-tools/charty/raw/master/images/design_concept.png)

## Installation

To be described later.

## Usage

```ruby
require 'charty'
charty = Charty.new(:matplot)

bar = charty.bar do
  series [0,1,2,3,4], [10,40,20,90,70]
  series [0,1,2,3,4], [90,80,70,60,50]
  series [0,1,2,3,4,5,6,7,8], [50,60,20,30,10, 90, 0, 100, 50]
  range x: 0..10, y: 1..100
  xlabel 'foo'
  ylabel 'bar'
  title 'bar plot'
end
bar.render("sample_images/bar_matplot.png")
```

Charty also supports Daru::DataFrame, Numo::NArray, NMatrix and ActiveRecord as Data Abstraction Layer.
For example.

```ruby
require 'charty'
charty = Charty::Plotter.new(:matplot)


### when Daru::DataFrame
require 'daru'
df = Daru::DataFrame.new({'a':[1,2,3,4], 'b':[4,5,6,7], 'c':[8, 9, 10, 11]})
charty.table = df


### when Numo::NArray
require "numo/narray"
narray = Numo::DFloat.new(3,5).seq
charty.table = narray


### when NMatrix
require "nmatrix"
nmatrix = NMatrix.new([3, 4], [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], dtype: :int64)
charty.table = nmatrix


### when ActiveRecord
require "active_record"
ActiveRecord::Base.establish_connection(adapter: "sqlite3", database: ":memory:")
ActiveRecord::Schema.define do
  create_table :foos do |t|
    t.integer :price
    t.integer :sales
  end
end
class Foo < ActiveRecord::Base
end
100.times{|i| Foo.create!(price: 10 * i, sales: (1..100).to_a.sample) }
sales = Foo.where("sales >= 40")
charty.table = sales


bar = charty.to_bar(:price, :sales)
bar.render('sample')

boxplot = charty.to_boxplot(:price, :sales)
boxplot.render('sample')

bubble = charty.to_bubble(:price, :sales, :id)
bubble.render('sample')

curve = charty.to_curve(:price, :sales)
curve.render('sample')

scatter = charty.to_scatter(:price, :sales)
scatter.render('sample')

errorbar = charty.to_errorbar(:price, :sales)
errorbar.render('sample')

hst= charty.to_hst(:price, :sales)
hst.render('sample')
```

## Examples

create an instance of the library you want to use.

```ruby
require 'charty'

# when you want to use matplotlib
charty = Charty.new(:matplot)

# when you want to use gruff
charty = Charty.new(:gruff)

# when you wanto to use rubyplot
charty = Charty.new(:rubyplot)
```

### Bar

```ruby
bar = charty.bar do
  series [0,1,2,3,4], [10,40,20,90,70]
  series [0,1,2,3,4], [90,80,70,60,50]
  series [0,1,2,3,4,5,6,7,8], [50,60,20,30,10, 90, 0, 100, 50]
  range x: 0..10, y: 1..100
  xlabel 'foo'
  ylabel 'bar'
  title 'bar plot'
end
bar.render("sample_images/bar_matplot.png")
```

#### Matplotlib

![bar matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/bar_matplot.png)

#### Gruff

![bar gruff](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/bar_gruff.png)

#### Rubyplot

![bar rubyplot](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/bar_rubyplot.png)

### Curve

```ruby
curve2 = charty.curve do
  series [0,1,2,3,4], [10,40,20,90,70]
  series [0,1,2,3,4], [90,80,70,60,50]
  series [0,1,2,3,4,5,6,7,8], [50,60,20,30,10, 90, 0, 100, 50]
  range x: 0..10, y: 1..100
  xlabel 'foo'
  ylabel 'bar'
end
curve2.render("sample_images/curve_matplot.png")
```

#### Matplotlib

![curve2 matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/curve_matplot.png)

#### Gruff

![curve2 gruff](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/curve_gruff.png)

#### Rubyplot

![curve2 rubyplot](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/curve_rubyplot.png)

### Curve with function

```ruby
curve = charty.curve do
  function {|x| Math.sin(x) }
  range x: 0..10, y: -1..1
  xlabel 'foo'
  ylabel 'bar'
end
curve.render("sample_images/curve_with_function_matplot.png")
```

#### Matplotlib

![curve matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/curve_with_function_matplot.png)

#### Gruff

Not supported

#### Rubyplot

![curve rubyplot](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/curve_with_function_rubyplot.png)

### Box plot

```ruby
boxplot = charty.boxplot do
  data [[60,70,80,70,50], [100,40,20,80,70], [30, 10]]
  range x: 0..10, y: 1..100
  xlabel 'foo'
  ylabel 'bar'
  title 'box plot'
end
boxplot.render("sample_images/boxplot_matplot.png")
```

#### Matplotlib

![box matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/boxplot_matplot.png)

#### Gruff

Not supported

#### Rubyplot

Not supported

### Scatter

```ruby
scatter = charty.scatter do
  series 0..10, (0..1).step(0.1), label: 'sample1'
  series 0..5, (0..1).step(0.2), label: 'sample2'
  series [0, 1, 2, 3, 4], [0, -0.1, -0.5, -0.5, 0.1]
  range x: 0..10, y: -1..1
  # xlabel 'x label'
  # xlabel ''
  ylabel 'y label'
  title 'scatter sample'
end
scatter.render("sample_images/scatter_matplot.png")
```

#### Matplotlib

![scatter matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/scatter_matplot.png)

#### Gruff

![scatter gruff](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/scatter_gruff.png)

#### Rubyplot

![scatter rubyplot](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/scatter_rubyplot.png)

### Errorbar
 
```ruby
errorbar = charty.errorbar do
  series [1,2,3,4], [1,4,9,16], xerr: [0.5,1.0,1.5,0.3], yerr: [0.6,0.2,0.8,0.1], label: 'label1'
  series [1,2,3,4], [16,9,4,1], label: 'label2'
  series [1,2,3,4,5,6,7,8], [14,14,14,14,14,14,14,14], label: 'label2', xerr: [0.5,1.0,1.5,0.3, 1.1, 1.2, 1.3, 1.4]
  range x: 0..10, y: -1..20
  xlabel 'x label'  
  title 'errorbar'
end
errorbar.render("sample_images/errorbar_matplot.png")
```

#### Matplotlib

![errorbar matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/errorbar_matplot.png)

#### Gruff

Not supported

#### Rubyplot

Not supported

### Bubble chart

```ruby
bubble = charty.bubble do
  series 0..10, (0..1).step(0.1), [10, 100, 1000, 20, 200, 2000, 5, 50, 500, 4, 40], label: 'sample1'
  series 0..5, (0..1).step(0.2), [1, 10, 100, 1000, 500, 100], label: 'sample2'
  series [0, 1, 2, 3, 4], [0, -0.1, -0.5, -0.5, 0.1], [40, 30, 200, 10, 5]
  range x: 0..10, y: -1..1
  xlabel 'x label'
  ylabel 'y label'
  title 'bubble sample'
end
bubble.render("sample_images/bubble_matplot.png")
```

#### Matplotlib

![bubble matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/bubble_matplot.png)

#### Gruff

Not supported

#### Rubyplot

![bubble rubyplot](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/bubble_rubyplot.png)

### Histogram

```ruby
hist = charty.hist do
  data [[10, 10, 20, 30, 40, 40,40,40,40,40, 50, 10, 10, 10], [100, 100, 100, 100, 90, 90, 80, 30, 30, 30, 30, 30]]
  range x: 0..100, y: 0..7
  xlabel 'x label'
  ylabel 'y label'
  title 'histogram sample'
end
hist.render("sample_images/hist_matplot.png")
```

#### Matplotlib

![hist matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/hist_matplot.png)

#### Gruff

Not supported

#### Rubyplot

Not supported

### Subplots

```ruby
layout = charty.layout
layout << curve
layout << scatter
layout.render("sample_images/subplot_matplot.png")
```

#### Matplotlib

![subplot matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/subplot_matplot.png)

#### Gruff

Not supported

#### Rubyplot

Not supported

### Subplots 2

```ruby
curve_list = [0.5, 0.75].map do |f|
  charty.curve(f:f) do
    function {|x| Math.sin(f*x) }
    range x: 0..10, y: -1..1
  end
end

scatter_list = [-0.5, 0.5].map do |f|
   charty.scatter(f: f) do
    series Charty::Linspace.new(0..10, 20), Charty::Linspace.new(0..f, 20)
    range x: 0..10, y: -1..1
  end
end

grid_layout = charty.layout(:grid2x2)
grid_layout << curve_list
grid_layout << scatter_list
grid_layout.render("sample_images/subplot2_matplot.png")
```

#### Matplotlib

![subplot2 matplotlib](https://raw.githubusercontent.com/red-data-tools/charty/master/examples/sample_images/subplot2_matplot.png)

#### Gruff

Not supported

#### Rubyplot

Not supported


## Acknowledgements

- The concepts of this library is borrowed from Python's [HoloViews](http://holoviews.org/) and Julia's [Plots ecosystem](https://juliaplots.github.io/).

## Authors

- Kenta Murata \<mrkn@mrkn.jp\>
- Kazuma Furuhashi \<k.furuhashi10@gmail.com\>

## License

MIT License

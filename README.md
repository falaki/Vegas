# Vegas

[![TravisCI](https://travis-ci.org/aishfenton/Vegas.svg?branch=master)](https://travis-ci.org/aishfenton/Vegas)
[![codecov.io](https://codecov.io/github/aishfenton/Vegas/coverage.svg?branch=master)](https://codecov.io/github/aishfenton/Vegas?branch=master)

Vegas aims to be the missing MatPlotLib for the Scala and Spark world. Vegas wraps around [Vega-Lite](https://vega.github.io/vega-lite/) but provides syntax more familar (and type checked) for use within Scala.

<img src="https://dl.dropboxusercontent.com/u/8245460/gallary.png" width="1021">

## Quick start

Add the following jar as an SBT dependacy

```sbt
libraryDependencies += "com.github.aishfenton" % "vegas_2.11" % "0.1.0"
```

And then use the following code to render a plot into a pop-up window (see below for more details on controlling how and where Vegas renders).

```scala
import vegas._
import vegas.render.WindowRenderer._

val chart = Vegas("Country Pop").
  withData(
    Map("country" -> "USA", "population" -> 314),
    Map("country" -> "UK", "population" -> 64),
    Map("country" -> "DK", "population" -> 80)
  ).
  encodeX("country", Nominal).
  encodeY("population", Quantitative).
  mark(Bar)

chart.show
```

!["Readme Chart 1"](https://dl.dropboxusercontent.com/u/8245460/readme-chart-1.png)

See further examples [here](http://nbviewer.jupyter.org/github/aishfenton/Vegas/blob/master/docs/Examples.ipynb)

## Rendering

Vegas provides a number of options for rendering charts out to. The primary focus is using Vegas within interactive notebook environments, such as Jupyter and Zeppelin.

### Notebooks

#### Jupyter - Scala

If you're using [jupyter-scala](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=jupyter%20scala), then you must incldue the following in your notebook before using Vegas.

```scala
load.jar("../core/target/scala-2.11/vegas-assembly-0.1.0.jar")

import vegas._
import vegas.render.HTMLRenderer._
implicit val displayer: String => Unit = display.html(_)
``` 

#### Jupyter - Apache Toree

And if you're using [Apache Toree](https://toree.incubator.apache.org/), then this:

```
%addJar file:../core/target/scala-2.10/vegas-assembly-0.1.0.jar
```

```scala
import vegas._
import vegas.render.HTMLRenderer._
implicit val displayer: String => Unit = { s => kernel.display.content("text/html", s) }
``` 

#### Zeppelin

And lastly if you're using Apache Zeppelin [Zeppelin](https://zeppelin.incubator.apache.org/) then use the following to initialize the notebook.

```
%addJar file:../core/target/scala-2.10/vegas-assembly-0.1.0.jar
```

```scala
import vegas._
import vegas.render.HTMLRenderer._
implicit val displayer: String => Unit = { s => "%HTML " + s }
``` 

The last line in each of the above is required to connect Vegas to the notebook's HTML renderer (so that the returned HTML is rendered instead of displayed as a string). 

See a comprehensive list example notebook of plots  [here](http://nbviewer.jupyter.org/github/aishfenton/Vegas/blob/master/docs/Examples.ipynb)

### Standalone

Vegas can also be used to produce standalone HTML or even render plots within a built-in display app (useful if you wanted to display charts for a command-line-app).

#### HTML or JSON

The following renders the plot as both HTML (which is printed to the console), and as JSON containing the Vega-lite spec, which can copy-and-pasted into the Vega-lite [editor](https://vega.github.io/vega-editor/?mode=vega-lite&spec=bar).

```scala
import vegas._
import vegas.render.HTMLRenderer._

val chart = Vegas("Country Pop").
  withData(
    Map("country" -> "USA", "population" -> 314),
    Map("country" -> "UK", "population" -> 64),
    Map("country" -> "DK", "population" -> 80)
  ).
  encodeX("country", Nominal).
  encodeY("population", Quantitative).
  mark(Bar)

println(chart.spec.pageHTML())
println(chart.spec.toJson())
```

#### Window

Vegas also contains a self-contained display app for displaying plots (internally JavaFX's HTML renderer is used). The following demonstrates this and can be used from the command line. 

```scala
import vegas._
import vegas.render.WindowRenderer._

val chart = Vegas("Country Pop").
  withData(
    Map("country" -> "USA", "population" -> 314),
    Map("country" -> "UK", "population" -> 64),
    Map("country" -> "DK", "population" -> 80)
  ).
  encodeX("country", Nominal).
  encodeY("population", Quantitative).
  mark(Bar)

chart.show
```

## Spark integration

Vegas comes with an optional extension package that makes it easier to work with Spark DataFrames and RDDs. First you'll need an extra import

```sbt
libraryDependencies += "com.github.aishfenton" % "vegas_2.11" % "0.1.0"
```

```scala
import vegas.spark.SparkExt._
```

This adds the following new methods: 

* withData(df: DataFrame)
* withData(rdd: RDD[Product])

In the first case, each DataFrame column is exposed as a field keyed using the column's name. In the second case, an RDD of _case classes_ is expected, and reflection is used to map the case class's fields to fields within Vegas.   

## Plot Options

TODO

## Contributing

See [here](abc) for more information on contributing bug fixes and features.


##Lets make a bar chart

Say you have a little data, an array of numbers:
	var data = [4, 8, 15, 16, 23, 42];

A bar chart is simple yet perceptually-accurate way to visualize such data. Thisintroductory tutorial covers how to make abar chart using the D3 JavaScript library. First we'll make a bare-bones version in HTML, then a more complete chart in Scalable Vector Graphics(SVG), and lastly animated transitions between views.

This tutorial assumes you know a little about web deve;p[,emt: hot to edit a web page and view it in your browser, how to load the D3 library, and the like. You might find it convenient to fork this CodePen template to get started.

##Selecting an Element

In vanilla JavaScript, you typically deal with elements one at a time. For example, to create a div element, set it contents, and then append it the body:

	var div = document.createElement("div");
	div.innerHTML = "Hello, world!;
	document.body.appendChild(div);

With D3 (as with jQuery and other libraries), you instead handle groups of related elements called sections. Working with elements en masse gives selections their power; you can manipulate a single element or many of them without substantially restructuring your code. Although this may seem like a small change, eliminating loops and other control flow can make your code much cleaner.

A selection can be created in a myriad of ways. Most often you create one by querying a selector, which is a special string that identifies desired elements by name or class("div" or ".foo", respectively). While you can create a selection for a single element:

	var body = d3.select("body");
	var div = body.append("div");
	div.html("Hello, world!");

You can just as easily perform the same operation on many elements:

	var section = d3.selectAll("section");
	var div = section.append("div");
	div.html("Hello, world");

##Chaining Methods

	Another convenience of selections is method chaining: selecting methods, such as selection.attr, return the current selection. This lets you easily apply multiple operations to the same elements. To set the text color and backgroundcolor of the body without method chaining, you'd say:

	var body = d3.select("body");
	body.style("color", "black")
	body.style("background-color", "White");
	

"Body, body, body!" Compare this to method chaining, where the repetition is eliminated:

	d3.select("body")
		.style("color", "black")
		.style("background-color", "white");

Note we didn't even need a var for the selected body element. After applying any operations, the selection can be discarded. Method chaining lets you write shorter code (and waste less time fretting over variable names).

There is gotcha with method chaining, however: while most operations return the same selection, some methods return a new one! For example, selection.apprend returns a new selection containing the new elements. This conveniently allows you to chain operations into the new elements.

	d3.selectAll("section")
		.attr("class", "special")
		.append("div")
		.html("Hello, world!");

Since method chaining can only be used to descend into the document hierarchy, use var to keep references to selections and go back up.

	var section = d3.selectAll("section");

	section.append("div")
		.html("First!");

	section.append("div")
		.html("Second!.");

##Coding a Chart, Manually

Now consider how you might create a bar chart without JavaScript. After all, there are only six numbers in this trivial data set, so it's not hard to write a few div elementas by hand, set their width as a multiple of the data, and be done with it.

<html>
	<!DOCTYPE html>
		<style>
		.chart div {
		  font: 10px sans-serif;
		  background-color: steelblue;
		  text-align: right;
		  padding: 3px;
		  margin: 1px;
		  color: white;
		}
		</style>
	<body>
		<div class="chart">
		  <div style="width: 40px;">4</div>
		  <div style="width: 80px;">8</div>
		  <div style="width: 150px;">15</div>
		  <div style="width: 160px;">16</div>
		  <div style="width: 230px;">23</div>
		  <div style="width: 420px;">42</div>
		</div>
	</body>
</html>

This chart has one div for a container, and one child div for each bar. THe child divs have a blue background color and a white foreground color, giving the appearence of bars with right-aligned value labels. You could simplify this implementation even further by removing the containing chart div. But more commonly your page will contain content in addition to the chart, so having a chart container lets you position and style the chart without affecting the rest of the page.

##Coding a Chart, Automatically

	Of course hard-coding is impractical for most datasets, and the point of this tutorial is to teach you how to create charts from data automatically. So now let's create the identical structure using D3, starting with an empty page that contains only a dic of class "chart". The following script selects the chart container and then appends a child div for each bar with the desired width:

		d3.select(".chart")
			.selectAll("div")
				.data(data)
			.enter().append("div")
				.style("width", function(d) { return d * 10 + "px" })
				.text(function(D) { return d; });

		Although partly familiar, this code introduces an important new concept - the data join. Let's break it down, rewriting the above concise code in long form, to see how it works.

		First, we select the chart container using a class selector.

			var chart = d3.select(".chart");

		Next we initiate the data join by defining the selection to which we will join data.

			var bar = chart.selectAll("div");

		Since we know the selection is empty, the returned update and exit selections are also empty, and we need only handle the enter selection which represents ne wdata for which there was no existing element. We instantiate these missing elements by appending to the enter selection.

			var barEnter = barUpdate.enter().append("div");

		Now we set the width of each new bar as a multiple of the associated data value, d.

			barEnter.style("width", function(d) { return d * 10 + "px"; });

		Because these elements were created with the data join, each bar is already bound to data. We set the dimensions of each bar based on it data by passing a function tp compute the width style property. Lastly, we use a function to set the text content of each bar, and produce a label.

			barEnter.text(function(d) { return d; });

		D3's selection operators such as attr, style and property, allow you to specify the value either as a constant (the same for all selected elements) or a function (computed separately for each element). If the value of a particular attribute should be based on the element's associated data, then use a function to comput it; otherwise, if it's the same for all elements, then a string or number suffices.

##Scaling to Fit

One weakness of the code above is the magic number 10, which is used to scale the data value to the appropriate pixel width. This number depends on the domain of the data (the minimum and maximum value, 0 and 42 respectively), and the desired width of the chart(420), but of course these dependencies are only implicit in the value 10.

We can make these dependencies and eliminate the magic number using a linear scale. D3's specify a mapping from data space(Domain) to display space(range):

	var x = d3.scale.linear()
		.domain([0, d3.max(data)])
		.range([0, 420]);

Although x here looks like an object, it is also function that returns the scaled display value in the range for a given data value in the domain. For example, an input value of 4 returns 40, and an input value of 16 returns 160. To use the new scale, simply replace the hard-coded multiplication by call the scale function:

	d3.select(".chart")
		.selectAll("div")
			.data(data)
		.enter().append("div")
			.style("width", function (d) { return x(d) + "px"; })
			.text(function(d) { return d; });




		
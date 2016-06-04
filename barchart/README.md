#Lets make a bar chart

Say you have a little data, an array of numbers:
	var data = [4, 8, 15, 16, 23, 42];

A bar chart is simple yet perceptually-accurate way to visualize such data. Thisintroductory tutorial covers how to make abar chart using the D3 JavaScript library. First we'll make a bare-bones version in HTML, then a more complete chart in Scalable Vector Graphics(SVG), and lastly animated transitions between views.

This tutorial assumes you know a little about web deve;p[,emt: hot to edit a web page and view it in your browser, how to load the D3 library, and the like. You might find it convenient to fork this CodePen template to get started.

#Selecting an Element

In vanilla JavaScript, you typically deal with elements one at a time. For example, to create a div element, set it contents, and then append it the body:

	var div = document.createElement("div");
	div.innerHTML = "Hello, world!;
	document.body.appendChild(div);

With D3 (as with jQuery and other libraries), you instead handle groups of related elements called sections. Working with elements en masse gives selections their power; you can manipulate a single element or many of them without substantially restructuring your code. Although this may seem like a small change, eliminating loops and other control flow can make your code much cleaner.

A selection can be created in a myriad of ways. Most often you create one by querying a selector, which is a special string that identifies desired elements by name or class("div" or ".foo", respectively). While you can create a selection for a single element:

##Chaining Methods

	Another convenience of selections is method chaining: selecting methods, such as selection.attr, return the current selection. This lets you easily apply multiple operations to the same elements. To set the text color and backgroundcolor of the body without method chaining, you'd say:

	var body = d3.select("body");
	body.style("color", 
	div.innerHTML = "Hello, world!");
	div.html("Hello, world");

You can just as easily perform the same operation on many elements:

	var section = d3.selectAll("section");
	var div = section.append("div");
	div.html("Hello, world!");

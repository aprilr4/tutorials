Now that you know how to work with data in JavaScript, and how to alter the page using the DOM, it's time to put the two together and create data-driven web pages.

So far you've created pages where you've typed in all of the content by hand, but in many cases, you will want at least some of that content to be derived from data that are stored separately and change over time. Wouldn't it be cool if you could dynamically build some of your page's content based on some data you have in JavaScript as an array of objects? Well, you're in luck; the DOM provides us mechanisms to do just that.

## Creating, Populating, and Appending New Elements

In the previous tutorial and challenge you learned how to alter existing elements in your page, but the DOM also allows us to create new elements that don't yet exist. This is done using the `document.createElement()` method:

```javascript
//create a new <p> element
var myNewPara = document.createElement("p");

//set its text content
myNewPara.textContent = "Hello World!";
```

The `document.createElement()` method creates a new disconnected, free-floating DOM element in memory, and we can manipulate it just like any other DOM element, but it won't appear on screen until we actually add it to the page. To add it to the page, we need to append it as a child element of some existing element in the tree. For example, say we want that new paragraph element to appear at the bottom of our page. If we append it as a new child of the `<body>` element, it will appear at the very bottom of the body, after all existing child elements:

```javascript
//select the <body> element
var body = document.querySelector("body");

//append our new <p> element as a new child of <body>
body.appendChild(myNewPara);
```

The [`.appendChild()`](http://www.w3schools.com/jsref/met_node_appendchild.asp) method is available on all DOM elements, so you can append new elements as children of any existing element on your page. The `.appendChild()` method will always append your new element to the *end* of the existing children list, but if you would rather add it *before* some existing child, you can use the [`.insertBefore()`](http://www.w3schools.com/jsref/met_node_insertbefore.asp) instead.

There is also a corresponding [`.removeChild()`](http://www.w3schools.com/jsref/met_node_removechild.asp) to remove a specific child element from another element. But if you simply want to clear *all* content from a given element (child elements and/or text), it's much faster and simpler to set the element's `.textContent` property to an empty string (`""`);

## HTML Tables

If we want to display tabular data in our web pages, the most appropriate elements to use are those that create an HTML table. Like a spreadsheet, an HTML table presents tabular data in rows and columns. Each row should present data from one record or observation, and each column should present one property from each record or observation. Columns should be labeled with a title, and may also have an optional footer for displaying totals or other statistics for the column.

HTML tables have a similar element structure to HTML pages. They start with a single element named `<table>`, which contains two elements: one for the table header (column headings); and one for the table body (the data rows).

```html
<table>
	<thead>
		<!-- column headings -->
	</thead>
	<tbody>
		<!-- table rows -->
	</tbody>
</table>
```

The `<thead>` element should contain one table row element (`<tr>`), and that row should contain one table header element (`<th>`) for each column you want to display:

```html
<table>
	<thead>
		<tr>
			<th>First Name</th>
			<th>Last Name</th>
			<th>Age</th>
		</tr>
	</thead>
	<tbody>
		<!-- table rows -->
	</tbody>
</table>
```

By default, browsers will render `<th>` elements in bold, but you can override this using CSS, just like any other element.

The `<tbody>` element should contain one table row element for each data row, and each row should contain table data cell elements (`<td>`) for each column. For obvious reasons, the number of `<td>` elements per row should match the number of `<th>` elements in the `<thead>` section. If there is no data for the column, simply leave the contents of the `<td>` element blank.

```html
<table>
	<thead>
		<tr>
			<th>First Name</th>
			<th>Last Name</th>
			<th>Age</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>John</td>
			<td>Smith</td>
			<td>35</td>
		</tr>
		<tr>
			<td>Jane</td>
			<td>Lee</td>
			<td>29</td>
		</tr>
	</tbody>
</table>
```

When the browser renders the table, it will ensure that all of the columns line-up, regardless of how much content happens to be within a given cell. By default, the content in each cell will be left-aligned, but you can adjust that using CSS. For example, it's common to set the CSS property `text-align: right` on elements containing numeric content so that the numbers are right-aligned. You can define one CSS style class that applies this formatting, and then add that style class to any `<td>` that displays numeric data, as well as the `<th>` for that column:

```css
.numeric {
	text-align: right;
}
```

```html
<table>
	<thead>
		<tr>
			<th>First Name</th>
			<th>Last Name</th>
			<th class="numeric">Age</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>John</td>
			<td>Smith</td>
			<td class="numeric">35</td>
		</tr>
		<tr>
			<td>Jane</td>
			<td>Lee</td>
			<td class="numeric">29</td>
		</tr>
	</tbody>
</table>
```

## Building Tables from Data

Now suppose we wanted to build this HTML table dynamically from data. If we know the shape of the data ahead of time, we could continue to hard-code the `<thead>` and `<th>` elements, as those remain the same regardless of the data rows, but leave the `<tbody>` empty, appending one row per record in our data.

For example, say we had the data from above in a JavaScript array of objects:

```javascript
var people = [
	{
		fname: "John",
		lname: "Smith",
		age: 35,
		email: "jsmith@example.com",
		description: "..."
	},
	{
		fname: "Jane",
		lname: "Lee",
		age: 29,
		email: "jlee@example.com",
		description: "..."
	}
];
```

This creates an array with two objects, each of which has the same set of properties: `fname`, `lname`, `age`, `email`, and `description`. This is conceptually similar to the `MOVIES` array you worked with in an earlier challenge.

Our goal is create one `<tr>` for each record, with `<td>` elements for each of the properties. The best way to do that is to break up the task into a few reusable functions. The fist would be a function that can create, populate, and return a new `<td>` element given some value and a flag to indicate whether the value should be formatted as numeric:

```javascript
function renderTableCell(value, isNumeric) {
	//create the new <td> element
	var td = document.createElement("td");

	//set its text content to the provided value
	td.textContent = value;
	
	//if it should be formatted as numeric...
	if (isNumeric) {
		//add the "numeric" style class
		td.classList.add("numeric");
	}

	//return the new element to the caller
	return td;
}
```

The function name starts with `render` because that's what we typically call functions that transform raw data into some kind of content that can be shown to a human.

The next function you need is one that can create a new `<tr>` element and populate it with associated `<td>` elements, given one of the records, which is a JavaScript object.

```javascript
function renderPersonRow(person) {
	//create the <tr> element
	var tr = document.createElement("tr");

	//create and append the <td> elements
	tr.appendChild(renderTableCell(person.fname));
	tr.appendChild(renderTableCell(person.lname));
	tr.appendChild(renderTableCell(person.age, true));

	//return the table row to the caller
	return tr;
}
```

Here we use our existing `renderTableCell()` function to create the various `<td>` elements. Each time we pass a different property from the person record as the `value` parameter. For the first two, we omit the second `isNumeric` parameter, but we set it to `true` on the last call, as that property should be formatted as a number. Omitted parameters in JavaScript are automatically set to `undefined`, and that [will evaluate as `false`](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md#truthy--falsy) when we test it with the `if ()` condition, so omitting the parameter is the same as passing `false`.

These two functions will create a fully populated `<tr>` element in-memory, but we still need to append that `<tr>` to the `<tbody>` in order for it to be displayed on the page. That requires a third function, which accepts the entire array of people records, loops over the array elements, calling `renderPersonRow()` for each, and appending the returned table row to the `<tbody>` element:

```javascript
function renderPeopleTable(peopleArray) {
	//select the <tbody> element
	//you can make this more precise by using a descendant selector,
	//referring to a particular <table> by ID or style class name
	var tbody = document.querySelector("tbody");

	//clear any existing content in the body
	tbody.textContent = "";

	//for each element in the array...
	for (var idx = 0; idx < peopleArray.length; idx++) {
		//get the person record at the current index
		var person = peopleArray[idx];
		
		//render that person record as a <tr> with <td>s
		//and append it to the <tbody>
		tbody.appendChild(renderPersonRow(person));
	}
}
```

Now we can call our `renderPeopleTable()`, passing the array of records, and it will dynamically populate the `<tbody>` based on whatever happens to be in the array:

```javascript
//var people = [...] from above
renderPeopleTable(people);
```

## Using Blocks of HTML as Templates

Tables have a fairly simple and rigid structure, so it's easy to just create `<tr>` and `<td>` elements as you run through an array of data. But suppose you wanted to render these people using something like an MDL card layout, which requires significantly more HTML per-record. You could create all of the requisite elements, just as we did with the table rows and cells, but it might be easier to define a template instead.

A **template** is a block of HTML that you want to repeat for each record in your data array. The HTML could be quite complex, with lots of nested `<div>` elements and style classes, but only the elements that actually contain data would need to change for each of the data records. You can define this block of HTML once in your page, and then use JavaScript to clone, populate, and append it for each data record. 

For example, say you had this block of HTML in your web page:

```html
<!-- template for each person record -->
<div hidden id="person-template" class="mdl-card mdl-shadow--2dp">
	<div class="mdl-card__title">
		<h2 class="person-name mdl-card__title-text"></h2>
	</div>
	<div class="person-description mdl-card__supporting-text"></div>
	<div class="mdl-card__actions mdl-card--border">
		<a class="contact-person-button mdl-button mdl-button--colored mdl-js-button mdl-js-ripple-effect" href="#">
			Contact
		</a>
	</div>
</div>
```

There are a few things to note about this HTML block. First, the outermost `<div>` element has an attribute named `hidden`. This tells the browser not to display this block of HTML on the page, and it tells assistive technologies like screen readers to ignore it. This content is simply a template that we will clone and populate later for each record, so we don't want the empty template itself showing up on screen.

Second, I've added particular style class names to the various elements within this block that will show data values. For example, the `<h2>` element has a style class named `person-name` in addition to all the other MDL-specific style classes. This will allow us to find and populate these elements easily after we clone the template. It will also make it easy to style the data.

To use this template, we need to write a little JavaScript. Our first function will render one record given a template:

```javascript
function renderPersonCard(person, template) {
	//deep clone the template
	var card = template.cloneNode(true);

	//remove the `hidden` attribute from the clone 
	//so that it will be displayed
	card.removeAttribute("hidden");

	//populate the various descendant elements with data
	//person name
	var fullName = person.fname + " " + person.lname;
	card.querySelector(".person-name").textContent = fullName;

	//person description
	card.querySelector(".person-description").textContent = person.description;

	//contact button link
	card.querySelector(".contact-person-button").href = "mailto:" + person.email;

	//return the cloned, populated card
	return card;
}
```

Every DOM element has a [`.cloneNode()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/cloneNode) method that will create a copy of the element. If you pass `true` as the first parameter, it will create a **deep copy**, which means it makes a copy of all the descendant elements as well. When you create a deep copy, the returned elements are completely new and separate from the ones you cloned. Modifying the copy won't alter the original template.

But when we copy, we get all attributes and child elements, including the `hidden` attribute on the template. We don't want this on the clone as we intend to populate the clone and show it on screen, so the second line of code removes that attribute from the copy.

We then use our style classes to find the descendant elements whose text content should be set to the data values. Here we use the `.querySelector()` method on the clone element so that it only searches the descendants of that element, and not the entire document.

This function will create a single card from a record and template, so to create all cards for all records, we need another function:

```javascript
function renderPeopleCards(peopleArray, template) {
	//create a <div> to hold all of the cards
	var div = document.createElement("div");
	
	//for each data record...
	for (var idx = 0; idx < peopleArray.length; idx++) {
		//get the person record at the current index
		var person = peopleArray[idx];
		
		//create and append a card
		div.appendChild(renderPersonCard(person, template));
	}

	return div;
}
```

This function creates a new `<div>` to hold all of the cards, and then just loops through the array, calling `renderPersonCard()` for each element, and appending the returned card to the `<div>`.

To call this, we just need to select our template element, and pass it as the second parameter:

```javascript
//select the element with id="person-template"
var template = document.querySelector("#person-template");
var cards = renderPersonCards(people, template);

//append the cards to some existing element so that they appear
var main = document.querySelector("main");
main.appendChild(cards);
```

## Templating Libraries

This process of merging data with a template is so common that several JavaScript libraries have been created to make the process even easier. These libraries use special syntax to indicate where data values should appear, and many support simple conditionals and looping/repeating right in the template itself.

A simple and popular templating library is [Handlebars](http://handlebarsjs.com/). The template above would look like this when using Handlebars:

```html
<!-- handlebars library -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.0.6/handlebars.min.js"></script>

<!-- our template -->
<script id="person-template" type="text/x-handlebars-template">
	<div class="mdl-card mdl-shadow--2dp">
		<div class="mdl-card__title">
			<h2 class="mdl-card__title-text">
				{{fname}} {{lname}}
			</h2>
		</div>
		<div class="mdl-card__supporting-text">
			{{description}}
		</div>
		<div class="mdl-card__actions mdl-card--border">
			<a class="mdl-button mdl-button--colored mdl-js-button mdl-js-ripple-effect" 
				href="mailto:{{email}}">
				Contact
			</a>
		</div>
	</div>
</script>
```

Because Handlebars extends the HTML syntax, they prefer you to put Handlebars templates inside a `<script type="text/x-handlebars-template">` element. The browser will not interpret this as JavaScript, but will instead ignore it, figuring that some other program will interpret the contents later. It will also be ignored by HTML validators.

Note how the template contains several `{{...}}` expressions. These double curly braces denote something that Handlebars will replace with data when the template is merged. If you tilt your head 90 degrees, the double curly braces will look like handlebar mustaches, hence the name of the library!

To use this template, we first use the Handlebars library to compile it:

```javascript
//get the contents of the id="person-template" element
var source = document.querySelector("#person-template").innerHTML;

//compile that into a function that we can use to create
//fully-populated copies of the template
var templateFunc = Handlebars.compile(source);
```

The variable `templateFunc` is actually a function that accepts a JavaScript data object as its first parameter. The function will merge that data with the template, returning the fully-populated HTML as a string.

```javascript
//copy and populate the template, returning HTML as a string
var cardHTML = templateFunc(people[0]);

//select an element you want to contain the generated HTML
//and set its .innerHTML property
var main = document.createElement("main");
main.innerHTML = cardHTML;
```

Calling `templateFunc()` causes Handlebars to merge the data passed as the first parameter with the template. Everyplace you have `{{...}}` in your template, Handlebars evaluates the [expression](http://handlebarsjs.com/expressions.html) between the curly braces against the data object you passed as the first parameter. Typically these are just property names (e.g., `fname`, `email`), and Handlebars will replace the entire `{{...}}` expression with the value of that property. As Handlebars does this, it automatically escapes any HTML that might be in the property, so the results will be as safe as if you had set the element's `.textContent` property. 

The Handlebars template function returns the merged HTML as a string, so it's not yet a real DOM element. To make it display on screen, select an existing element and set its `.innerHTML` property. Or you can create a new `<div>` element, set its `.innerHTML` property, and append it to some existing element.

## Other Templating Libraries

Handlebars is just one of many templating libraries in the JavaScript world, but they all operate on the same basic principles. The most popular these days, but also the most complex, is [React.js](https://facebook.github.io/react/). They take the approach of embedding the template right in your JavaScript, using a special syntax extension they've defined named JSX (JavaScript Expressions). Using this requires special build tools, so it's far beyond a Beginning Web Development Course. But if you're interested in learning more about React, see their [Tutorial](https://facebook.github.io/react/tutorial/tutorial.html) as well as their documentation on [Installation](https://facebook.github.io/react/docs/installation.html), [JSX](https://facebook.github.io/react/docs/introducing-jsx.html), and [Components and Props](https://facebook.github.io/react/docs/components-and-props.html).

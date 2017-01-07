Once we start building our web pages from data, we can then enable dynamic sorting and filtering of that data. If we crate functions to render an array of objects into HTML, all we need are some more functions to sort/filter that array, and re-render. Thankfully, JavaScript has us covered. JavaScript arrays support methods for sorting, filtering, iterating, mapping, and joining.

## Sorting

JavaScript arrays have a built-in `.sort()` method which will sort the array elements in-place. That is, it *modifies* the existing array, rearranging the elements so that they are in a sorted order.

By default, this method will sort the array elements alphabetically. This works fine for arrays of strings. For example:

```javascript
var names = ["Dave", "Ada", "JinHa", "Sean", "Harry"];
names.sort();
console.log(names); // ["Ada", "Dave", "Harry", "JinHa", "Sean"]
```

But sorting alphabetically is not so good if your array is full of numbers. For example:

```javascript
var nums = [1,10,21,2];
nums.sort();
console.log(nums); // [1, 10, 2, 21] ...whoops!
```

If your array contains anything besides strings, then you must give the `.sort()` method some help. You need to tell it how to compare pairs of elements against each other. Given that, the sorting algorithm can do the rest.

The way you do this is to pass a compare function to the `.sort()` method. This is similar to how you passed an event listener function to the `.addEventListener()` method of a DOM element. The sort algorithm will call your compare function multiple times, passing two elements at a time.

Your compare function must accept two parameters. Both will be set to elements in the array, and your function needs to compare those two elements against each other. If the first element should come *before* the second element in the sorted order, then your function must return a negative number. If the first element should come *after* the second element in the sorted order, then your function must return a positive number. And if both elements are equal to each other, and therefore should be *next* to each other in the sorted order, your function must return a zero.

For example, sorting that array of numbers could be done like so:

```javascript
function compareNums(n1, n2) {
	return n1 - n2;
}

var nums = [1,10,21,2];
nums.sort(compareNums);
console.log(nums); // [1, 2, 10, 21] ...yay!
```

The `compareNums()` function will be called multiple times by the sorting algorithm, and each time it will be passed two of the array elements. Since the array elements are just numbers, the function simply returns the first minus the second. If the first number is less than the second, the result will be a negative number. If the first number is greater than the second, the result will be a positive number. And if the two numbers are equal, the result will be zero. That's exactly what the compare function is supposed to return!

Just as with the event listeners, we can pass this compare function by referring to its name, or we can just pass the function as an in-line anonymous function value: 

```javascript
var nums = [1,10,21,2];
nums.sort(function(n1, n2) {
	return n1 - n2;
});
console.log(nums); // [1, 2, 10, 21] ...yay!
```

The results are exactly the same. The former approach allows you to define the function once and use it in multiple places, but the latter is more concise if you only end up using the function once.

Sorting arrays of objects is done the exact same way, except that the two elements passed to your compare function will be objects instead of simple numbers. Your compare function can then decide which property of the objects to compare. For example, say we had an array of people objects and we wanted to sort them by age ascending. The code would look like so:

```javascript
var people = [
	{
		name: "Fred",
		age: 29
	},
	{
		name: "Mary",
		age: 35
	},
	//...
];

//sort by the age property
people.sort(function(p1, p2) {
	return p1.age - p2.age;
});
```

The array contains a list of objects, each of which has the same properties: `name`, and `age`. The `.sort()` method can't know which of those properties we want to sort by, so our compare function specifically accesses the `.age` property. We use the same subtraction trick, but we subtract the `.age` property from each object. 

To sort by age descending, just reverse the subtraction order:

```javascript
//sort by the age property descending
people.sort(function(p1, p2) {
	return p2.age - p1.age;
});
```

Now the function will return a positive number if the age of p1 is less than p2, zero if they are equal, and negative if the age of p1 is greater than p2. Thus the records with the larger values in their `.age` properties will appear first in the sorted order rather than last.

## Filtering

You can also filter any JavaScript array, selecting just a subset of array elements you want to keep. As opposed to sorting, the `.filter()` method returns a new array containing just the elements you wanted. It doesn't modify the existing array, which is good. That allows you to get back to the full list of elements, since the original array remains intact.

Like the `.sort()` method, the `.filter()` method needs some help from you. It needs a function that will test each array element to determine whether it should be included in the new filtered array. This function takes one argument, which will be an array element, and it should return `true` if the element should be included in the filtered array, or `false` if it should not.

For example, say we wanted to filter our `people` array for only those under 30. The code would look like so:

```javascript
var underThirty = people.filter(function(p) {
	return (p.age < 30);
});
console.log(underThirty); //only those with .age < 30
console.log(people); //full set--remains unmodified
```

The filtering algorithm will call your test function once for each element in the array, passing that element as the first parameter. In this case, we simply return whether the `.age` property is less than `30`. If it is, the return value will be `true` and the element will be included. If it's not the return value will be `false` and it won't be included.

The test can be as complex as you need it to be. You just need to return `true` or `false` at the end.

## Iterating

So far you've used the standard `for ()` loop to iterate arrays, but there's another way to iterate arrays in JavaScript: the `.forEach()` method. Like `.filter()` this method takes a function that will be called once for each element in the array, but unlike `.filter()` the `.forEach()` method doesn't care what you return from that function, and it doesn't construct and return a new array. It just calls your function once for each element in the array.

For example, suppose you wanted to write the name of each person to the console. You could do that using a standard `for ()` loop, or you could use the `.forEach()` method.

```javascript
//standard for loop technique
for (var idx = 0; idx < people.length; idx++) {
	console.log(people[idx].name);
}

//.forEach() technique
people.forEach(function(p) {
	console.log(p.name);
});
```

The two techniques yield the exact same results, but the latter is a little more compact and less error-prone than the former. As you might expect, the `.forEach()` method is actually implemented using a standard `for ()` loop. Here is a rough version of how the `.forEach()` method is implemented:

```javascript
//adding something to Array.prototype makes it available
//on every array you create
Array.prototype.forEach = function(callbackFunc) {
	//`this` refers to the current array
	for (var idx = 0; idx < this.length; idx++) {
		callbackFunc(this[idx]);
	}
}
```

## Mapping

Another handy method on every array is `.map()`. This method is used to *transform* the elements in an array, returning a new array with all of the transformed values. It accepts a mapping function that will be called once for each element in the array. Whatever that function returns will be put into the output array at the same index.

For example, say I want to extract just the ages from my `people` array so that I get just an array of integers. I can do that using `.map()`:

```javascript
var ages = people.map(function(p) {
	return p.age;
});
console.log(ages); //[29, 35, ...]
```

Our function is called once for each object in the array, and our function returns only the `.age` property. So the resulting output array will contain the same number of elements as `people`, but each element will be just the value of the `age` property.

The mapping function can do whatever it wants to do, but whatever it returns will be in the output array at the same index as the input array element.

## Joining

The last array method to mention is `.join()`. This will concatenate all of the array elements into one long string and return it. You can specify what you want in between each element by passing a single string parameter to `.join()`. For example, to get all of names from our `people` array in a comma-delimited list, you could combine `.map()` with `.join()`:

```javascript
var names = people.map(function(p) {
	return p.name;
});
console.log(names.join(", ")); // "Fred, Mary, ..."
```

## Putting it all Together

Since all of these methods return a new array or a reference to the original array, you can combine these together into one function call chain. For example, say I wanted to get an alphabetically-sorted, comma-delimited list of names of those under 30. I could that with a few helper functions, and one function chain:

```javascript
function underThirty(p) { return (p.age < 30); }
function justName(p) {return p.name; }

var names = people.filter(underThirty)
				.map(justName)
				.sort()
				.join(",");

console.log(names);
```

The `people.filter()` method returns a new array containing only the person data objects that have an `.age` property less than 30. Since that is an array, it has a `.map()` method, and we call that to extract just the `.name` property from each object. That returns a new array with just strings for elements, and since that is an array, it has a `.sort()` method. Since the array just contains string, we can let it sort alphabetically, so there's no need to supply a compare function. And since the `.sort()` method conveniently returns a reference to the sorted array, you can call that array's `.join()` method to concatenate all of the elements into one string, separated by commas.

This technique is known as **functional programming** and it's become all the rage in the JavaScript community. Functional programming can be quite complicated to learn at first, but once you figure it out, you can accomplish a lot with a small amount of code. Your code also become much more testable, which makes it more reliable in the long-run. Various libraries have been developed around this paradigm, the most popular being [Lazy.js](http://danieltao.com/lazy.js/docs/).


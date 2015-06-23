# reve
Declarative DOM manipulations. Play happy with server-side render architecture, such as [scrat-seo](https://github.com/scrat-team/scrat-swig).

## Downloads

- [reve.js](https://raw.githubusercontent.com/switer/reve/master/dist/reve.js)
- [reve.min.js](https://raw.githubusercontent.com/switer/reve/master/dist/reve.min.js)

## Examples

- [HackerNews WebApp](https://github.com/switer/scrat-seo-reve-hackernews)

## Guide

Assume we have a `app`'s HTML that is generated by server:

```html
<div id="app" 
	r-show="{show}" 
	r-class="{hl: hl}"
>Hi, Reve</div>
```

Then create an the `app` ViewModel instance with it's element selector:

```js
var $app = new Reve({
	el: '#app',
	data: function () {
		// define some state of ViewModel
		return {
			show: true,
			hl: true
		}
	},
	ready: function () {
		// ... do something when instance compele ...
	},
	methods: {
		// ... define methods of the ViewModel ...
	}
})
```

After instancing, the element is rendered to:

```html
<div id="app" class="hl">Hi, Reve</div>
```

We can change states of the `app` ViewModel, such as disable the highlight state, 
then call `$update` method, it will update UI automatically:

```js
ready: function () {
	this.$data.hl = false
	// it need to call update() to trigger UI update after state being changed
	this.$update()
}
```

Render result: 

```html
<div id="app" class="">Hi, Reve</div>
```

If want to hide the `app` element, we can do it like this:

```js
this.$data.show = false
this.$update()
```

The `app` element's style of display will be "none":

```html
<div id="app" class="" style="display: none">Hi, Reve</div>
```

## API

#### Directives
Directive is declarative DOM manipulation, such as "r-class" is the DOM manipulation of add/remove class to the element.

- **r-show**

	set element's style of display to none, when value is false.  

- **r-class**

	Add className to the element when value is true, Otherwise remove that class.
	such as: 
	```html
	<span r-class="{
	  red    : hasError;
	  bold   : isImportant;
	  hidden : isHidden;
	}"></span>
	```

- **r-style**

	Set inline style to element.
	```html
	<span r-class="{
	  display    : show ? '':'none'
	}"></span>
	```

- **r-attr**

	Update element's attribute by binding data.
	```html
	<img r-attr="{src: imgUrl || './default.png'}" alt="">
	```

- **r-on**

	Add event listener to the element, such as add a "click" and "toucstart" events to the button element:
	```html
	<button 
		r-on="{
			click: onClick;
			touchstart: onTouchStart;
		}"
	></button>
	```

- **r-html**

	Update element's innerHTML by binding data.

- **r-component**

	Declare the element is a component root element, and Reve will instance it with specified component id.
	Assume we have defined a component with state "title" and method "capitalize", as below:
	```js
	Reve.component('header', {
		data: function () {
			return {
				title: ''
			}
		},
		methods: {
			capitalize: function (str) {
				var initial = str[0]
				return initial.towUpperCase() + str.slice(1)
			}
		}
	})
	```
	And component's template as below:
	```html
	<div id="app">
		<div r-component="header" 
			r-data="{
				title: "hi, reve"
			}""
		>
			<span r-html="{capitalize(title)}"></span>
		</div>
	</div>
	```
	It will be instanced when instance parent VM:
	```js
	var app = new Reve({
		el: '#app'
	})
	```
	Render result:
	```html
	<div id="app">
		<div>
			<span>Hi, reve</span>
		</div>
	</div>
	```

- **r-ref**

	Add a reference of the component instance to parent component instance.
	> Notice: work with "r-component" only.

	```html
	<div r-component="header" r-ref="header"></div>
	```
	We can access the header component instance refernce by parent VM's **$refs** property:
	```js
	app.$refs.header.$data.title // 'hi, reve'
	```

- **r-data**

	Passing and binding data from parent VM to child component.
	> Notice: work with "r-component" only.
	

- **r-methods**

	Passing methods from parent VM to child component.
	> Notice: work with "r-component" only.

#### Class Methods

- **Reve**(options)

	Create a ViewModel instance.

- **Reve.create**(options)

	Create a ViewModel Class.

- **Reve.component**(id, options)

	Create a ViewModel Class and assign `id`. So the ViewMode can be found with `r-component`


#### Instance Options

- **el** `<HTMLElement>` | `<String>`

	The root `Element` or element `Selector` of the ViewModel. 

- **template** `<String>`

	Replace ViewModel element's innerHTML with template, if el is not given, Reve will create a div element with the `template` innerHTML

- **data** `<Function>`

	Get default ViewModel's data, `data` is a method that should return an object.

- **methods** `<Object>`

	Specified methods of the ViewModel those can be use in directive's expression and be accessed by the ViewModel instance as property directly:
	```
	methods: {
		show: function () {
			// to do something
		}
	},
	ready: function () {
		this.show() // accessing as method property
	}
	```
- **created** `<Function>`

	**Lifecycle Method** It will be called before compiling directives of the ViewModel.

- **ready** `<Function>`

	**Lifecycle Method** It will be called after all directives of the ViewModel are compiled.

- **shouldUpdate** `<Function>`

	**Lifecycle Method** It will be called after each times calling `$update` method, 
	return `false` will stop current ViewModel to update UI.


#### Instance Properties

- **$el** `<HTMLElement>`

	The root element of the ViewModel instance.

- **$data** `<Object>`

	The data object of the ViewModel instance.

- **$methods** `<Object>`

	The methods option object of the ViewModel 

- **$refs** `<Object>`

	All child component instances those have declared the reference id with `r-ref`.

- **$directives** `<Object>`

	All directives those not contain child component's directives of the ViewModel.


#### Instance Methods

- **$update**()

	Call the method manually to update UI View when data of the ViewModel instance has been changed.

- **$compile**(`HTMLElement` | `String`)

	* return `<HTMLElement> | <DocumentFragment>` if param typeof `String`, it will be a documentfragment, otherwise will return HTMLElement.

	Compile all directives of the HTMLElement or HTML template in current ViewModel. It's useful when load something async then append to current ViewModel's DOM Tree.

## License

MIT







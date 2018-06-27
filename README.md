# createWebComponent
Library to simplify web component creation and reduce repetitive code for web components

## Usage
To use this library you will have to extend your web component class from the exported webComponentBaseClass:

```
import { webComponentBaseClass } from '../../src/webComponentBaseClass.js';

const componentName = 'my-element';
window.customElements.define(componentName, class extends webComponentBaseClass {
	static get is() { return componentName; }
	constructor() {
		super();
		// extra required initialization goes here ...
	}

	// here we add some properties to this web component
	static get properties() {
		return {
			propertyName: { // the name of the property
				type: String, // (required) the type of the property, one of Array, Boolean, Number, Object, String
				value: 'value', // (optional) default value for the property
				reflectToAttribute: true, // (optional) indicate if you want the component attribute to always reflect the current property value
				observer: '_myChangeHandler', // (optional) the name of a function in the class to be called when the value of the property is changed
			},
			// add as many properties as you need
		};
	}

	// optional callback function that will be called after this instance of the web component
	// has been added to the DOM
	attached() {
		this.addAutoEventListener(this.$.exampleInput, 'change', () => {
			this.$.output.textContent += `The input has changed to ${this.$.exampleInput.value}\n`;
		});
		this.$.output.textContent += `web component ${this.constructor.is} was attached to the DOM\n`;

		this.$$('.exampleElement').style.backgroundColor = 'red';
		this.$$$('.exampleElement span').forEach((p_Element, p_Index) => { p_Element.style.backgroundColor = (p_Index % 2) ? 'green' : 'blue'; });
	}

	// optional callback function that will be called after this instance of the web component has been removed from the DOM
	detached() {
		console.log(`web component ${this.constructor.is} was removed from the DOM`);
	}

	// change observer implementation example for a property
	_myChangeHandler(p_NewValue, p_OldValue) {
		this.$.output.textContent += `The component property 'propertyName' for web component ${this.constructor.is}, was changed from ${p_OldValue} to ${p_NewValue}\n`;
	}
});
```

The template definition for the web component should be constructed like this:
```
<!DOCTYPE html>
<template id="my-element">
	<!-- The id should match the componentName as specified in the javascript file -->
	<style>
		/* put you styling here */
		.exampleElement {
			position: relative;
			display: flex;
			flex-direction: column;
			margin: 1em;
		}

		.exampleElement span {
			box-sizing: border-box;
			display: block;
			position: relative;
			width: 100%;
			height: 20px;
		}

		#output {
			box-sizing: border-box;
			width: 100%;
			height: 300px;
			overflow: auto;
			box-shadow: inset 0 0 6px;
			padding: 1em;
		}
	</style>
	<!-- The content of the template goes here -->
	<div class="exampleElement">
		<span></span>
		<span></span>
		<span></span>
		<span></span>
	</div>
	<input id="exampleInput">
	<pre id="output"></pre>
</template>
<script>
	(function storeTemplate() {
		const template = document.currentScript.ownerDocument.querySelector('template');
		window.webComponentTemplates = window.webComponentTemplates || new Map();
		window.webComponentTemplates.set(template.getAttribute('id'), template);
	})();
</script>
<script type="module" src="./my-element.js"></script>
```

This library has examples provided which also can be used for creating your own web components.
See the examples directory in the repo.
# Mesibo JsonView

Create your own view without writing single code in your native platform. Mesibo provides an easy to load view just by modifying a MesiboJsonView structure, all you need to do is modify our MesiboJson strucutre as per your UI requirement and load it, in no time you will see the same UI you have designed in json. Get you own custom view in less than a minute no kidding. 

MesiboJsonView is designed to faciliate you with designing your own UI by just writing json. Pass json from server Mesibo will render it show your custom view.

## MesiboJsonView fetches this JSON markup from a server:
```javascript

{
	"mesibo_json": {
		"layouts": [{
			"type": "layout",
			"name": "topLayout",
			"orientation": "vertical",
			"gravity": "c",
			"style": {
				"padding": "10",
				"margin": "10",
				"border": "5",
				"borderColor": "#bdbdbd",
				"backgroundColor": "#ffffff",
				"width": "m",
				"height": "w"

			},
			"views": [{
				"type": "textView",
				"name": "nameTextView",
				"text": "Enter your name",
				"valueType": "string",
				"textColor": "#000000",
				"textSize": "20",
				"style": {
					"padding": "10",
					"margin": "10",
					"border": "0",
					"borderColor": "#42a5f5",
					"backgroundColor": "0",
					"textStyle": "bold",
					"width": "m",
					"height": "w"
				}
			}]
		}]
	}
}

```

## And self-constructs into the following view, in realtime:


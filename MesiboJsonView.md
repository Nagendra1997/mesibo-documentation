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
			.
			.
			.
			.
		}]
	}
}

```

## And self-constructs into the following view, in realtime:

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/json_normal_view.png" width="320" height="450">



## How it works

- Fetches Json markup from server
Json structure is sent form server so you are completely independent from coding, write your json and send it the app. 

- Itself construct into custom view
When json a received from server Mesibo Json view renders and construct all the given view by itself.

- Update the json
Simply update the json and see it in real time, No waiting.

## Cool things about MesiboJsonView

 - **100% Native**: MesiboJson view maps JSON into native components and native function calls. 
 - **Fast execution**: Just update json and its done.
 - **No Coding**: No need to code, basic knowledge of json structure will do the work.
 
 
 # Structure and Uses
  
**── mesibo_json ──**

A MesiboJson view document always starts with `mesibo_json` as its root node, and has a children: layouts, each of which has multiple children of its own.

**── layouts ──**

The layouts contains a set of views that gets displayed directly. This is a JsonArray of views.

`layouts` can contain the following attributes:

   1. type
   2. orientation
   3. gravity
   4. views
   
 In JSON they look like this:

```java

{
  "mesibo_json": {
   "layouts": [
      {
        "type": "layout",
        "orientation": "vertical",
        "gravity": "c",
        "style": {
         .
	 .
	 .
        },
        "views": [
	  .
	  .
	  .
	 
```	 
	  

	  





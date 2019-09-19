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

**── type ──**

Type of the layouts, generally put layout to have a Linear layout.

**── orientation ──**

Orientation of the layout, `vertical` - for vertical orientation and `horizontal` for horizontal orientation.

**── gravity ──**

Gravity is the layout gravity that you give in your Linear layout. Put `gravity` value as `s` - for gravity at `start`, `c` for `center` and `e` for gravity at `end`.

**── views ──**

`views` is an array of elements that needs to be displayed. It has following attributes

## Types of views

**1. textView**

TextView as it is clear from name, this type is used to display text in your custom view. 

```java
{
            "type": "textView",
            "name": "NameET",
            "text": "Enter your name",
            "value": "",
            "textColor": "#000000",
            "textSize": "20",
            "style": {
              "padding": "10",
              "margin": "10",
              "border": "0",
              "borderColor": "#42a5f5",
              "backgroundColor": "0",
              "textStyle": "normal",
              "width": "m",
              "height": "w"
            }
          }

```


**2. editText**

This type makes text to be editable. It helps in building the data interface taken from any user, also contains certain features through which we can hide the data which are confidential.

```java
	
	{
            "type": "editText",
            "name": "name",
            "text": "",
            "hint": "Type here",
            "hintTextColor": "#dcdcdc",
            "value": "",
            "valueType": "number",
            "maxLength": "10",
            "minLenth": "3",
            "valueError": "Max length 5",
            "textColor": "#42a5f5",
            "textSize": "15",
          }

```
	  
**3. button**

This is used to create a button. Buttons are very helpful in getting into a content. Android button represents a clickable push-button widget.

```java 

{
            "type": "button",
            "name": "name",
            "text": "Proceed",
            "value": "",
            "valueType": "string",
            "maxLength": "0",
            "minLenth": "0",
            "valueError": "Max length 5",
            "textColor": "#42a5f5",
            "textSize": "15",
	    "style": {
              "padding": "10",
              "margin": "10",
	     
	     .
	     .
	     .
	    }
}	    

```


**4. button**

This is used to create a button. Buttons are very helpful in getting into a content. Android button represents a clickable push-button widget.

```java 

{
            "type": "button",
            "name": "name",
            "text": "Proceed",
            "value": "",
            "valueType": "string",
            "maxLength": "0",
            "minLenth": "0",
            "valueError": "Max length 5",
            "textColor": "#42a5f5",
            "textSize": "15",
	    "style": {
              "padding": "10",
              "margin": "10",
	     
	     .
	     .
	     .
	    }
}	    

```

**5. image**

Image view helps to display images. Any image can be selected, we just have to pass image url in `url` attribute and Mesibo View will render and loads image in a image view.
```java 

{
            "type": "image",
            "name": "Image View",
            "imageUrl": "https://i.pinimg.com/originals/57/d6/18/57d618ca8019e488843795405db5b577.png",
	    "style": {
              "padding": "10",
              "margin": "10",
	     
	     .
	     .
	     .
	    }
}	    

```

**6. checkBox**

Checkbox is used in that applications where we have to select one option from multiple provided. Checkbox is mainly used when 2 or more options are present.
```java 

{
              
            "type": "checkBox",
            "name": "fruitsSelect",
            "text": "Apple",
            "textColor": "#000000",
            "textSize": "20",
	    "style": {
              "padding": "10",
              "margin": "10",
	     
	     .
	     .
	     .
	    }
}	    

```

**7. radioGroup & radioButton**

Radio button is like checkbox, but there is slight difference between them. Radio button is a  two-states button that can be either checked or unchecked. Since Radio buttons can be kept alone or in group, For group you need to keep radio buttons in radiogroup. RadioGroup has `orientation`- vertical/horizontal, this defines the orientation of the radio buttons.


```java 

{
            "type": "radioGroup",
            "name": "countdown",
            "orientation": "vertical",
            "rb": [
              {
                "type": "radioButton",
                "name": "countdown",
                "text": "Male",         
                "textColor": "#000000",
                "textSize": "20",
                "style": {
                  "padding": "10",
                  "margin": "10",
                  "border": "0",
                  "borderColor": "#42a5f5",
                  "backgroundColor": "0",
                  "textStyle": "normal",
                  "width": "m",
                  "height": "w"
                }
              },
              {
                "type": "radioButton",
                "name": "countdown",
                "text": "Female",
                "textColor": "#000000",
                "textSize": "20",
                "style": {
                  "padding": "10",
                  "margin": "10",
                  "border": "0",
                  "borderColor": "#42a5f5",
                  "backgroundColor": "0",
                  "textStyle": "normal",
                  "width": "m",
                  "height": "w"
                }
              }
            ],
            "style": {
              "padding": "10",
              "margin": "10",
              "border": "0",
              "borderColor": "#42a5f5",
              "backgroundColor": "0",
              "textStyle": "normal",
              "width": "m",
              "height": "w"
            }
          }

```
**8. line**

line is used to display a line, you can give width, height, color, etc. to this line. 
```java 

{
              
           {
            "type": "line",
            "style": {
              "margin": "15",
              "backgroundColor": "#dcdcdc",
              "width": "m",
              "height": "5"
            }
          },
}	    

```

**9. slider**

Slider is a horizontal view with its children scrollable in the view. You can keep any type of view as its children, it can be ImageView, TextView etc.
```java 

{
            "type": "slider",
            "name": "ImageSlider",
            "views": [
              {
                "type": "image",
                "name": "Image View",
                "imageUrl": "http://i.imgur.com/DvpvklR.png",
                "style": {
                  "padding": "0",
                  "margin": "15",
                  "border": "2",
                  "borderColor": "#bdbdbd",
                  "backgroundColor": "#ffffff",
                  "width": "w",
                  "height": "w"
                }
              },
              {
                "type": "image",
                "name": "Image View1",
                "imageUrl": "https://i.pinimg.com/originals/57/d6/18/57d618ca8019e488843795405db5b577.png",
                "style": {
                  "padding": "0",
                  "margin": "15",
                  "border": "2",
                  "borderColor": "#bdbdbd",
                  "backgroundColor": "#ffffff",
                  "width": "w",
                  "height": "w"
                }
              },
              {
                "type": "image",
                "name": "Image View2",
                "imageUrl": "http://i.imgur.com/DvpvklR.png",
                "style": {
                  "padding": "0",
                  "margin": "15",
                  "border": "2",
                  "borderColor": "#bdbdbd",
                  "backgroundColor": "#ffffff",
                  "width": "w",
                  "height": "w"
                }
              }
            ],
            "style": {
              "padding": "10",
              "margin": "10",
              "border": "5",
              "borderColor": "#42a5f5",
              "backgroundColor": "#ffffff",
              "textStyle": "normal",
              "width": "m",
              "height": "w"
            }
          }    

```

**10. spinner**

Spinner is used to display a dropdown menu of items from which user can select anyone, you need to pass items of the menu 'items' as array.
```java 

{
              
           {
            "type": "spinner",
            "name": "countdown",
            "text": "",
            "textColor": "#000000",
            "textSize": "20",
            "items": ["1","2","3","4","5"],
            "style": {
              "padding": "10",
              "margin": "10",
              "border": "2",
              "borderColor": "#000000",
              "backgroundColor": "0",
              "textStyle": "italic",
              "width": "m",
              "height": "w"
            }
          }
}	    

```




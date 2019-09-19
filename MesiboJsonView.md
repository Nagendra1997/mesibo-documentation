# Mesibo JsonView

Create your view without writing a single code in your native platform. Mesibo provides an easy to load view just by modifying a MesiboJsonView structure, all you need to do is modify our MesiboJson structure as per your UI requirement and load it, in no time you will see the same UI you have designed in JSON. Get your custom view in less than a minute no kidding. 

MesiboJsonView is designed to facilitate you with designing your UI by just writing JSON. Pass JSON from server Mesibo will render it show your custom view.

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
JSON structure is sent form server so you are completely independent from coding, write your JSON and send it to the app. 

- Itself construct into custom view
When JSON a received from server Mesibo Json view renders and construct all the given view by itself.

- Update the JSON
Simply update the JSON and see it in real-time, No waiting.

## Cool things about MesiboJsonView

 - **100% Native**: MesiboJson view maps JSON into native components and native function calls. 
 - **Fast execution**: Just update JSON and its done.
 - **No Coding**: No need to code, basic knowledge of JSON structure will do the work.
 
 
 # Structure and Uses
  
**── mesibo_json ──**

A MesiboJson view document always starts with `mesibo_json` as its root node, and has a children: layouts, each of which has multiple children of its own.

**── layouts ──**

The layouts contain a set of views that gets displayed directly. This is a JsonArray of views.

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

## **── views ──**

`views` is an array of elements that needs to be displayed. It has following attributes

## Types of views

**1. textView**

TextView as it is clear from the name, this type is used to display text in your custom view. 

Here,

`name` : Its kind of id of the view to find it while performing action.
`text` : Text that you want to display in Textview
`textColor` : Colour of the text. Pass hex color code.
`textSize` : Size of the text, Text size is set in dp.
`textAlignment` : Alignment of the text, `s` for alignment at the start, `c` for center and  `e` for alignment at end.

```java
{
            "type": "textView",
            "name": "NameET",
            "text": "Enter your name",
            "textColor": "#000000",
            "textSize": "20",
        "textAlignment": "c",
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

`hint` : Hint of the editText. 
`hintTextColor` : Color of the hint text.
`value' : This is the default value. If you need to pass a default value if the edit text is left empty.
`valueType` : Type of the value allowed to enter in the edit text field. These are - "string", "number" and "email".
`maxLength` : Number of maximum characters allowed to enter in the field.
`minLength` : Number of minimum characters allowed to enter in the field.
`valueError` : Error message if the entered characters are not in lie in between given max and min length.

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

This is used to create a button. Buttons are very helpful in getting into content. Android button represents a clickable push-button widget.

```java 

{
            "type": "button",
            "name": "name",
            "text": "Proceed",
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


**4. image**

Image view helps to display images. Any image can be selected, we just have to pass an image URL in `imageUrl` attribute and Mesibo View will render and loads an image in an image view.
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

**5. checkBox**

Checkbox is used in those applications where we have to select one option from multiple provided. Checkbox is mainly used when 2 or more options are present.
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

**6. radioGroup & radioButton**

Radio button is like a checkbox, but there is a slight difference between them. Radio button is a  two-states button that can be either checked or unchecked. Since Radio buttons can be kept alone or in a group, For group you need to keep radio buttons in radiogroup. RadioGroup has `orientation`- vertical/horizontal, this defines the orientation of the radio buttons.


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
**7. line**

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

**8. slider**

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

**9. spinner**

Spinner is used to display a dropdown menu of items from which user can select anyone, you need to pass items of the menu 'items' as array.
```java 

{
              
           {
            "type": "spinner",
            "name": "countdown",
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

## **── styles ──**

In every view you can pass a style object that changes the style of your view. In JSON it looks like this:- 
```java

 "style": {
                  "padding": "20",
                  "margin": "10",
                  "border": "15",
                  "borderColor": "#42a5f5",
                  "backgroundColor": "0",
                  "textStyle": "normal",
                  "width": "m",
                  "height": "w"
                }

```
`style` object has following attributes - 


**1. padding**

`padding` : Give padding to all sides equally. The value of the padding is taken in dp.

**2. margin**

`margin` : Give margin to all sides equally. The value of the padding are taken in dp.

**3. border**

`border` : Outline border is given using 'border'. The value of the border is the amount of round corners you want at all the four sides. Pass "border": "0" - To have rectangular outline or "border": "10" - 10 being the round corner value.

**4. borderColor**
Color of the outline border.


**5. backgroundColor**

Background color of the View, passing hex color value will set background of the view as given color.

**6. textStyle**

This style is given especially to TextView and EditText. `bold`, `italic` and `normal` are three values you can pass to change the text style accordingly.


**7. width**

Width of the view, you can pass `m` for full length as of the screen, `w` for wrapping around the view, as the size of the view, you can even pass width value in dp as "width": "5".

**8. height**
Height of the view to be displayed.



## **── actions ──**

However what makes Mesibo JsonView truly powerful is the ability to express functions that actually do something, entirely in JSON. We call it `action`. 

`action` is an array of actions, you can give action in array and Mesibo jsonview will perform all the action in the array one by one.

This could be anything from accessing the device features such as camera or address book, to making network requests. And you can compose them to carry out any sophisticated tasks.

Here, in JSON it looks like:

```java

  "actions": [{
                "type": "toast",
                "text": "Pop Music"
              },
              {
                "type": "alert",
                "text": "Proceed to next",
                "positiveText": "Yes",
                "negativeText": "Cancel"
              }
        ]
```



##### Syntax

Actions can take the following attributes

    type: Specify action type. (note: THIS CANNOT BE A TEMPLATE EXPRESSION that needs to be evaluated. Use a static string.)
    text: text you want to show when action is performed, it is applicable in few types of `action` only.

### Type of `action`

**1. toast**

`toast` can be used to display information for a short period. A `toast` contains message to be displayed quickly and disappears after some time. 

Attributes: 

`text` is the message to display.


```java

  {
                "type": "toast",
                "text": "This is Toast Text"
              }

```


**2. alert**

`alert` can be used to display the dialog message with positive and negative buttons. It can be used to interrupt and ask the user about his/her choice to continue or discontinue.

Attributes-

`text` is the message to display.
`positiveText`- A button to perform positive action.
`negativeText` -  Button to generally dismiss the alert box.

```java

 {
                "type": "alert",
                "text": "Proceed to next",
                "positiveText": "Yes",
                "negativeText": "Cancel"
              }

```

**3. validate**

This action is performed to validate the views with their validation parameters. Suppose if a textview is validated it will validate if the TextView is empty or not, Max. and Min characters of the TextView. Similarly, users can give their own validation criteria and Mesibo Json view will let you validate the given view.

Attributes-
`text` is the name of the view. `name` is provided to all the views, giving particular `name` in validation will validate that particular view which matched with the given `name`.

```java
 {
                "type": "validate",
                "name": "NameTextView"
              }
```

**3. send**

This action collects the values from all the views and sends it to the server. This is sent via mesibo message.

```java
{
                "type": "send"
              }
```


### Example
Lets see a few example how these all works together, We are going the see all the views in this example.


```java 



```


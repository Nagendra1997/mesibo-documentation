---
description: Mesibo Scripting Utilities 
keywords: chatbot, script, scripting
title: Utilities 
---

# Global Utility functions 
mesibo provides a set of utilities for generating a random number, hashing, etc.
## `random32()`  
Returns a 32-bit psuedo-random number. 

### Syntax

```javascript
random32()
```
### `Parameters`
None.

### `Return value`
`unsigned 32-bit` integer 

## `random64()`  
Returns a 64-bit psuedo-random number. 

### Syntax

```javascript
random64()
```
### `Parameters`
None.

### `Return value`
`unsigned 64-bit` integer 

## `hash64()`  
Returns a hash of the input. 

### Syntax

```javascript
hash64()
```
### `Parameters`
Any valid object

### `Return value`
A 64 bit hash value 

## `print()`  
Utility to print to console window.
Prints given comma seperated arguments, to console seperated by space.

### Syntax

```
print(string_1, string_2, ... , string_N)
```

### `Parameters`
Comma seperated strings

### `Return value`
`undefined`

### Example

```javascript
print("Hello", "Mesibo");
//Logs 'Hello Mesibo' to console window
```

### Timing Utilities

## `startTimer()`
Start a timer for specified number of seconds and execute the event handler defined in `mesibo.ontimeout`.
The next call to `startTimer()` will reset the timer.

### Syntax

```
startTimer(tms)
```

### `Parameters`
`tms` :  The value of the time after which the timeout event will be triggered, in milliseconds

### `Return value`
`undefined`

### Example

```javascript
mesibo.ontimeout = function () {
	print("Timer fired");
}

startTimer(3000);
//timeout handler will be fired after three seconds
```

## `timeInMicros()`
Return the number of microseconds since the UNIX epoch. 

### Syntax

```
timeInMicros()
```
### `Parameters`
None
### `Return value`
A number representing the microseconds elapsed since the UNIX epoch.

## `timeInMillis()`
Return the number of milliseconds since the UNIX epoch. 

### Syntax

```
timeInMillis()
```
### `Parameters`
None
### `Return value`
A number representing the milliseconds elapsed since the UNIX epoch.

## `timeInSeconds()`
Return the number of seconds since the UNIX epoch. 

### Syntax

```
timeInSeconds()
```
### `Parameters`
None
### `Return value`
A number representing the seconds elapsed since the UNIX epoch.

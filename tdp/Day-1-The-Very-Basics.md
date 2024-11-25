The simplest thing you can do is make Houdini talk back to you. The first thing we are going to do, is go up to the shelf tab for our package. You will see tool already called "Reload Module", as well as one called "Node Info". Don’t mind these for now, and right click in the empty space and create a new shelf tool.

> Shelf tools can store and run code just the way they are, but it’s good practice to store the code in a file in your package, import it and run it from the shelf tool.

### **Change the path at the top to the location of the shelf tool.**
This is very important. It ensures that the script is stored in the package folder too and accessible by anyone who has the package installed.

Let’s come up with a good name for our script, for this example, let’s call it `TDPUtils`. In the code block, we write:

```python
import TDPUtils

TDPUtils.greet()
```

You may be wondering what this means, where is `TDPUtils` and where is it being imported from?

Well, let’s open our package folder in our favorite code editor, and go to `/scripts/python` and create our file called `TDPUtils.py`.

You may have noticed, in our shelf tool, we used `TDPUtils.greet()`, this is when we make the `greet` function in our file:

```python

def greet():
	print("Hey there!")

```

> Now, a caveat of Houdini, all of these files are loaded when it starts, so clicking our shelf tool will fail to run now. Restart Houdini before the next step.

Click our shelf tool, and we should see a small window pop up that says… "Who is there".

Congratulations, you have made your first shelf tool! 

Click here for some notes about reloading these files so you can avoid restarting Houdini every time you make a change.
## Reloading the package

The "Reload Module" shelf tool acts as a button that will refresh all the loaded python files in the module. This is crucial for ensuring Houdini is reading the updated files you have been editing in your code editor. After any changes in your files, you can click this button and all your changes will be reloaded into Houdini.

## Using arguments
Now that you have made your first function, one might think of how and where these could be used, and often times a bit of modularity is required too. Let's take the example of our `greet` function: How can we add more functionality? Running with our greet function, we can adjust the function to accept an argument. Let's call it `first_time`:

```python
def greet(first_time):
	if first_time:
		print("Hey there!")
	else:
		print("Hello again!")
```

We added a condition to check if the first time argument is `True`, and if we reload and run now, we will get an error. We need to adjust our shelf tool to call the function with the argument:

```python
import TDPUtils

TDPUtils.greet(True)
```

Now, if we pass `True`, it replies "Hey there!", and if we pass `False`, we get "Hello again!".

This is a simple example, but the concept of setting up logic and passing information from Houdini to our script is the foundation of everything we can do with Python in Houdini.

### **Prompt**: Create a function that takes the currently selected nodes, and prints each of them to the console.
## Extra Info

When we create our function, we can set it up to accept an **optional argument**, where we set a default value for the argument, and if the argument is omitted, it will use that value:

```python
def greet(first_time=True):
	if first_time:
		print("Hey there!")
	else:
		print("Hello again!")
```
Now if we call `greet()` without the argument, we print "Hey there!".
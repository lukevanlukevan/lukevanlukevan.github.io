### Lets's recap what we know so far:

We know how to create a script and run it in Houdini, how to use arguments in our functions, how to loop through multiple nodes.
We know how to create nodes, how to connect them to other nodes.
We know how to set parameters, how to set the expressions, and how to get paths of other parameters and nodes.
## Kwargs

Now that you can say with confidence, "*I know how to create a script, import it and run it*", you may find yourself wondering where else you can launch these scripts from. We will get to that, but the first thing we need to familiarise ourselves with is the concept of `kwargs`.

Let's create a new function in our `TDPUtils.py` file, and call it `get_kwargs()`:

```python
def get_kwargs(kwargs):
	print(kwargs)
```

You will notice that here we have added a required argument called `kwargs`. In most cases, anywhere you can run a script from, there will be a dictionary named `kwargs`.  In our shelf tool, we can import `TDPUtils` and run `TDPUtils.get_kwargs(kwargs)`.

When we run this, we will get this in the console:
```python
{'toolname': 'tool_1', 'panename': '', 'altclick': False, 'ctrlclick': False, 's
hiftclick': False, 'cmdclick': False, 'pane': None, 'viewport': None, 'inputnode
name': '', 'outputindex': -1, 'inputs': [], 'outputnodename': '', 'inputindex': 
-1, 'outputs': [], 'branch': False, 'autoplace': False, 'requestnew': False}
```

It's not pretty, but we can see some really useful information here, such as which modifier keys are used, and a lot of empty info too. The level of information here can depend on where you run it from. 

Let's modify our function to loop through the dict, and make it a bit easier to read:

```python
def get_kwargs(kwargs):
	for k,v in kwargs.items():
		print(f"{k}: {v}")
```

Using `dict.items()` is a common way to get the name of the item, as well as the value into 2 variables that you can print.

Now, let's set up my second most common way of running scripts:

Let's create a simple HDA, just an empty subnet saved as a digital asset ([go here if you dont know how to do this](https://www.sidefx.com/docs/houdini/assets/create.html)).

Let's right click it, and click "Type Properties". This will open a menu, and we can go to the parameters tab, and on the left we can drag a button to the right hand side. We set the name and label for the button, in my case, "execute".

Then, we can jump over to the "Scripts" tab. At the bottom left, there is a dropdown menu called "event Handler". lick that and then click on "Python Module". This created a script on the left called `PythonModule`, on the right, we can now write a script. As you know by now, I like to keep all scripts on disk, but for this example, let's write a quick function that prints our `kwargs`. We can copy paste the one we used previously:

```python
def get_kwargs(kwargs):
	for k,v in kwargs.items():
		print(f"{k}: {v}")
```

We can put this in the `PythonModule` section, but now we need to run it from our HDA. Let's jump back to our "Parameters" tab, and click on our button parameter. Here we can click on the "Callback Script" section, and in it, we can write:

```python
hou.phm().get_kwargs(kwargs)
```

If we then click Apply, and Accept, we can then click the button on our HDA, and it will print our `kwargs`. So, we have now run our script in the `PythonModule` from the button. We know our `get_kwargs` function exists in our `TDPUtils` file, so we can adjust our `PythonModule` script to import it and run it, but it feels like too many places just to get the code to run.
For this, we can adjust the script that is in the Callback Script, to import and run from there. The caveat is that if we press "Enter" for a new line, it doesn't create it. So, what we can do is this:

Place our cursor in the "Callback Script" box, and press Alt+E, to open it in the expression editor. From here, we can use our line breaks just fine. Write:

```python
import TDPUtils
TDPUtils.get_kwargs(kwargs)
```

When we click Accept, the window closes and the line updates to: `import TDPUtils¶TDPUtils.get_kwargs(kwargs)` (this character wont copy and paste, don't try to copy it).

The other option is to write the line like this:

```python
import TDPUtils; TDPUtils.get_kwargs(kwargs)
```

Using either method, we can now run our script file straight from the button, without needing the `PythonModule`, which is nice and clean, and we now have access to our `kwargs`.
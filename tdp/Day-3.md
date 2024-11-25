If you did the prompt for Day 2, you may have found `node.moveToGoodPosition()`, here's what I used:
```python
def create_output_null():
	nodes = hou.selectedNodes() # We get our selected items
	for node in nodes:
		new_name = "OUT_" + node.name()
		output = node.createOutputNode("null", node_name=new_name)
		output.moveToGoodPosition()
```

### Lets's recap what we know so far:

We know how to create a script and run it in Houdini, how to use arguments in our functions, how to loop through multiple nodes.
We know how to create nodes, how to connect them to other nodes.
## Parameters

If we assume we have our node stored as `node` in a script, we can get a parameter by doing `node.parm("parm_name")`. Here is the [docs page](https://www.sidefx.com/docs/houdini/hom/hou/OpNode.html#parm) for `node.parm()` and if you click on it, you will see "-> hou.Parm". This shows what the function returns, and its worth clicking through to [hou.Parm](https://www.sidefx.com/docs/houdini/hom/hou/Parm.html) and seeing all the useful stuff there is to learn.

Create a Transform Sop and select it. 
A fundamental thing to understand with getting a parameter, is that we get the `hou.Parm` back, and from there, we can get its value, get its value, etc.

Create a function in `TDPUtils.py` called `get_parms` and make a shelf tool that calls it.

Now, let's loop through all selected nodes (this is a good thing to use, even if you only need one node, as it allows you to adapt it to work for multiple nodes if needed). A node like Transform that has a vector parameter, with multiple values, is still technically 3 parameters under the hood. Mousing over the label will reveal: `Parameters: tx ty tz`. In this case, let's just work on `tx`

```python
def get_parms():
	for node in hou.selectedNodes():
		our_parm = node.parm("tx")
		print(our_parm)
```

If we run the script with the transform selected, it will print: `<hou.Parm tx in /obj/geo1/transform2>`. As we know, this is a `hou.Parm`, and if we want to get the value, we can change our script:

```python
def get_parms():
	for node in hou.selectedNodes():
		our_parm = node.parm("tx")
		value = our_parm.eval()
		print(value)
```

We use `eval()` to evaluate the parameter. This will read the value at the current frame, and evaluate any expressions we have set for the parameter (`$FF` for example).

We can also set a parameter just as easily:
```python
def get_parms():
	for node in hou.selectedNodes():
		our_parm = node.parm("tx")
		value = our_parm.eval()
		our_parm.set(value*2)
```

In this case, `our_parm` is just a reference to `node.parm("tx")`, so we can call the `.set` function on it, to set the parameter to whatever we pass as the argument.

## Parameter Expressions

Now that we have set a parameter, we also set the expression, just as we could do when typing in the parameter box. Let's take the `tx` parameter, and set it to `ty` and multiply it by 2.

In this case, we don't want to set it to *the value* of `ty`, but specifically set it as a reference to `ty`. There are other `hou.Parm` methods we can use, but for the sake of learning, we will be doing it manually.

We will need to create 2 variables, one being our `tx` parameter, the other being our `tx` parameter:

```python
def get_parms():
	for node in hou.selectedNodes():
		tx = node.parm("tx")
		ty = node.parm("ty")
```

Now, if we think about how this would be written manually in the `tx` box, it would be: `ch("ty")*2` (This is a crude example as the paths are on the same node, but we will expand on it later).

If we read the docs page for `parm.setExpression`, we can see the only required argument is a string that is the expression, and we will build that expression in our script. What we need is the `path` of the parameter.

```python
def get_parms():
	for node in hou.selectedNodes():
		tx = node.parm("tx")
		ty = node.parm("ty")
		ty_path = ty.path()
		print(ty_path)
```

In my case, I get `/obj/geo1/transform1/ty` which is the **absolute path** of the parameter. For now, we will use this to construct our reference (some fun in Extra Info). Let's add to our script to build the reference:

```python
def get_parms():
	for node in hou.selectedNodes():
		tx = node.parm("tx")
		ty = node.parm("ty")
		ty_path = ty.path()
		ref_string = 'ch("' + ty_pat3 + '") * 2'
		print(ref_string)
```

If we run this, it prints `ch("/obj/geo1/transform1/ty") * 2`, which is just what we need. The last step is to set the expression on the parm:

```python
def get_parms():
	for node in hou.selectedNodes():
		tx = node.parm("tx")
		ty = node.parm("ty")
		ty_path = ty.path()
		ref_string = 'ch("' + ty_pat3 + '") * 2'
		tx.setExpression(ref_string)
```

Congratulations, you have set a parameter expression!

### **Prompt:** Create a workflow script for yourself that involves creating a node and setting a parameter value/expression.

## Extra Info

`hou.Parm` has some really useful methods, such as `parm.node()` which returns the path of the node the parameter exists on. We can use this to run a script using the parameter we right click on, or we can use this to change our absolute path above to a relative one.

Here is the same script, but we use `node.relativePathTo()` to get the path required, and we construct the string using [f-strings](https://www.geeksforgeeks.org/formatted-string-literals-f-strings-python/):

```python
def get_parms():
	for node in hou.selectedNodes():
		tx = node.parm("tx")
		ty = node.parm("ty")
		
		start_node = tx.node()
		start_name = tx.name()

		end_node = ty.node()
		end_name = ty.name()

		rel_path = start_node.relativePathTo(end_node)

		ref_string = f'ch("{rel_path}/{end_name}") * 2'
		tx.setExpression(ref_string)
```



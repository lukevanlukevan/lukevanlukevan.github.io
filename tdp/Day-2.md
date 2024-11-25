If you did the prompt for Day 1, you may have ended up with a function that looks something like this:
```python
def print_nodes():
	nodes = hou.selectedNodes()
	print(nodes)
	# or maybe
	for node in nodes:
		print(nodes)
```

The second bit, looping through our nodes is also one of the bread and butter workflows when doing simpler stuff, especially in the start, where most of our interests lie in speeding up tedious workflows.

### Lets's recap what we know so far:

We know how to create a script and run it in Houdini, how to use arguments in our functions, how to loop through multiple nodes.
## Nodes

As showcased above, getting a node can be done with a few methods, the easiest of which is going to be the selected nodes. This is also common, as often you will want to operate on these. If you did the above, you would see it prints some gibberish as well as some recognizable info, such as the node name. The correct thing to do now would be to go and read through the docs page for [hou.OpNode](https://www.sidefx.com/docs/houdini/hom/hou/OpNode.html) and [hou.Node](https://www.sidefx.com/docs/houdini/hom/hou/Node.html) (the difference is explained in todays extra info).

For this exercise, we will make a simple script to create an output null for any node we have selected. To start, we can work in our `TDPUtils.py` file, and create a new function. I will call it `create_output_null`. It's good to be as verbose as possible with naming, as things can get complex fast, and being vague doesn't help anybody.

```python
def greet(first_time=True):
	if first_time:
		print("Hey there!")
	else:
		print("Hello again!")

def create_output_null():
	pass
```

We left `pass` there, which essentially means "do nothing". Now that we have this ready, we can quickly make a new shelf tool, and make it call our function:

```python
import TDPUtils
TDUtils.create_output_node()
```

> From this point on, the creating of shelf tools won't be explained again, so save this area if its something you need to come back to.

Now, we can go back to our script, let's walk through creating our function.

```python
def create_output_null():
	node = hou.selectedNodes()[0]
	print(type(node))
```

Here we use `[0]` to get the first selected node (more on this later, don't worry).
Here, we print `type(node)`. This will give us the `type` of the data stored in the variable. 
So now that we have our node, and the print statement has confirmed our node type to be `hou.SopNode` (Just a variation of `hou.OpNode`)  it's a good time to go and read the [docs page for hou.OpNode](https://www.sidefx.com/docs/houdini/hom/hou/Node.html#createOutputNode) and see what functions are available to us.

We will be using `.createOutputNode()`, which has the required arguments listed. Remember how we create optional arguments in yesterday's code? Take note of which arguments are optional in this case. The only required one, is `node_type_name`. In our case, we want to create a null, so create a null manually, and click the shelf tool "Node Info". This will print the node info for us to use, specifically "Type Name: null", so we can build our script now:

```python
def create_output_null():
	node = hou.selectedNodes()[0]
	node.createOuputNode("null")
```

Now, if we select a node and run this, it will create an null, and connect it to the output of our node. This works well for a single node, which we have selected with our `[0]` bit, but now the next step is to do it for all of our nodes.

Let's first adjust our script and run it:

```python
def create_output_null():
	nodes = hou.selectedNodes() # We get our selected items
	print(type(nodes))
```

It is crucial to know that what we get back here is: `<class 'tuple'>`. More reading on this in the extra info, as well as [here](https://www.geeksforgeeks.org/python-data-types/). The bottom line is that this is a list of nodes, and in python, we can loop through a list/tuple very easily with by writing `for item in list_of_items`. Let's modify our script to run through each item:

```python
def create_output_null():
	nodes = hou.selectedNodes() # We get our selected items
	for node in nodes:
		node.createOutputNode("null")
```

Now, when we select a bunch of nodes, and run the script, we get a null for each node.
Knowing that inside of the loop we are operating on each node one by one, we can go a step further and set the name of the null to the name of our selected node:

```python
def create_output_null():
	nodes = hou.selectedNodes() # We get our selected items
	for node in nodes:
		new_name = "OUT_" + node.name()
		output = node.createOutputNode("null", node_name=new_name)
```

> We pass `node_name` explicitly here, by writing `node_name=new_name`, but technically as its the second argument, we can just write `node.createOutputNode("null", new_name)`, but it is good practice to keep track of what arguments you are passing to the functions

Here, we create a `new_name` variable to store the name, as well as store the null as `output`. You will use this if you do the prompt for today.

Now, we have a clean null named "Out_" for each of our nodes. Congratulations, you have connected some nodes! Bind this shelf tool to a hotkey and you have a useful workflow enhancer.

Some more info on creating nodes:
`createOutputNode` is only one way to make a node, there are many more hidden in the docs.
`node.createNode()` is a bread an butter function for creating a node, but it doesn't handle making connections for us like we had before. As always, the docs are contain a wealth of information, but I will outline a quick way to create a node and connect it to our selected node:

```python
def create_output_null():
	node = hou.selectedNodes()[0] # we are only getting the first node
	new_name = "OUT_" + node.name()
```

A really important thing here, is that when you create a node, it exists inside its parent. For example. a simple Box node may have a path like `/obj/geo1/box1`. The node's parent is `geo1`, and we use this to create the node. An analogy: In a **book** you have **page** and on the page you have a **word**. Logically, if life were Python, you would call `page.createWord()` rather than `word.createWord()` as words don't "contain" other words.

```python
def create_output_null():
	node = hou.selectedNodes()[0] # we are only getting the first node
	new_name = "OUT_" + node.name()
	parent = node.parent() # get the parent node

	null = parent.createNode("null", node_name=new_name)
```

Now, the node is created, but it is not connected. There are helper functions for [setting the first input](https://www.sidefx.com/docs/houdini/hom/hou/Node.html#setFirstInput), but for this example, I will use the long winded approach to explain it:

```python
def create_output_null():
	node = hou.selectedNodes()[0] # we are only getting the first node
	new_name = "OUT_" + node.name()
	parent = node.parent() # get the parent node

	null = parent.createNode("null", node_name=new_name)
	null.setInput(0, node)
```

Here, we call `null.setInput`, which takes the number of the input on the node, as well as the node to connect to it.
### **Prompt**: The output nulls aren't laid out nicely, so read through the docs page for `hou.Node` and see you can find a function to add to the loop that will _move a node to a good position_.

## Extra Info

### hou.Node vs hou.OpNode

Since Houdini 20.0, hou.Node has been further split up into sub-classes. This is due to the fact that different options may be available depending on whether a node is a sop node, obj node, etc. Most of the stuff exists on hou.Node, but if you can't find info you're looking for, it is worth checking out the specific node page for the node you are working on.

### List vs Tuple

For the case above, the only real difference between the list and the tuple is that you won't be able to directly modify the tuple, and should rather recreate it as a list (with `list()`) if you need to mess with it.

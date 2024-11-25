### Lets's recap what we know so far:

We know how to create a script and run it in Houdini, how to use arguments in our functions, how to loop through multiple nodes.
We know how to create nodes, how to connect them to other nodes.
We know how to set parameters, how to set the expressions, and how to get paths of other parameters and nodes.
We know how to access the `kwargs` dict, and use it in our scripts to get specific info related to context we run the script.
## Menus

If we look at this [docs page](https://www.sidefx.com/docs/houdini/basics/config_menus.html), we can see all of the places we can inject tools into the menus throughout Houdini. I will be using `OPMenu.xml`, which is the menu we see when we right click on a node. I will not be going into the complexities of placement in the menu right now, so just copy and paste this block, and save it as `OPMenu.xml` in our root TDP folder:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<menuDocument>
	<menu>
		<subMenu id="tdp_menu">
		<label>TDP</label>
			<scriptItem id="TDP_kwargs">
				<label>TDP Get Kwargs</label>
				<scriptCode><![CDATA[
import TDPUtils
TDPUtils.get_kwargs(kwargs)
				]]> </scriptCode>
			</scriptItem>
		</subMenu>
	</menu>
</menuDocument>
```

> You may notice that the formatting here is *horrible*, sadly this is just how the indentation needs to be for the python scripts to work

Now that we have saved this file, we can click the "Reload Module" shelf tool, and if we right click on a node, we will see a "TDP" menu at the bottom of the list (it may be in a different location for you, but see Extra Info for more).

Here, we created a submenu, and inside that, a scriptItem, which is our single "Get Kwargs" script. 

If we run this script, we can see it will print the `kwargs` again. but this time, very different info comes through:

```python
networkeditor: <hou.NetworkEditor panetab7>
commonparent: True
networkeditorpos: (-1.1045277812617589, 0.7014172892917081)
items: [<hou.SopNode of type box at /obj/geo1/box1>]
node: box1
toolname: h.pane.wsheet.TDP_kwargs
altclick: False
ctrlclick: False
shiftclick: False
cmdclick: False
```

Two very useful things here are that we get `node`, and we get the `networkEditor`. We aren't going to use network editor here, just `node`.

Now that we know our script can take `kwargs` as an argument and get this info, we can work easily with the node without having to get it through `hou.selectedNodes()`.

This was a simpler way to get the node, but doesn't seem as useful as our next use-case: the parameter menu.

Take our `OPMenu.xml` and duplicate it. We will rename it to `PARMmenu.xml`. Adjust the contents as below:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<menuDocument>
	<menu>
		<subMenu id="tdp_menu">
		<label>TDP</label>
			<scriptItem id="TDP_rand_inp_att">
				<label>TDP Randomize Input Attribute</label>
				<scriptCode><![CDATA[
import TDPUtils
TDPUtils.randomize_input_attribute(kwargs)
				]]> </scriptCode>
			</scriptItem>
		</subMenu>
	</menu>
</menuDocument>
```

We can see here that we now need a function called `randomize_input_attribute`, but first, lets thing of the logic:

Let's say you've dropped down an Attribute Remap, and want to add some variation to the attribute before we remap it; so what info do we need?

Our current node, our current parameter, and our current parameter value.

Let's create the function and first read the `kwargs` we get from it:

```python
def randomize_input_attribute(kwargs):
	get_kwargs(kwargs)
```

> Because we are in the `TDPUtils` file, we can simply call our other functions in the script and use them there, rather than having to rewrite them. I use this to get the pretty version of our `kwargs`.

This prints:

```python
parms: (<hou.Parm inname in /obj/geo1/attribremap1>,)
toolname: h.pane.parms.TDP_rand_inp_att
altclick: False
ctrlclick: False
shiftclick: False
cmdclick: False
```

You will see we get `parms`, which is a tuple of `hou.Parm`, but only contains our parameter. Using what we learned about `hou.Parm`, we know we can just use `parm.node()` to get the node. Let's build out the base of our function:

```python
def randomize_input_attribute(kwargs):
	parm = kwargs['parms'][0]
	node = parm.node()
	att_name = parm.eval()

	print(att_name)
```

Now we can right click the "Original Name" in our Attribute Remap, and run our script. It should print our attribute name (make sure you set it).

Using our new connection and creation knowledge, we can make an attribute noise and connect it between our nodes:

```python
def randomize_input_attribute(kwargs):
	parm = kwargs['parms'][0]
	node = parm.node()
	att_name = parm.eval()

	current_connection = node.input(0)

	noise = node.createInputNode(0, "attribnoise::2.0")

	noise.setInput(0, current_connection)
```

Now, all of our connections are made. We had to get the `current_connection` to reconnect the nodes in order, otherwise the Attribute Noise would have no input. We get the variable before we connect it, otherwise the `input` changes.

All that is left is to set the parameter to the value we stored, and set the attribute type to "Float"

```python
def randomize_input_attribute(kwargs):
	parm = kwargs['parms'][0]
	node = parm.node()
	att_name = parm.eval()

	current_connection = node.input(0)

	noise = node.createInputNode(0, "attribnoise::2.0")

	noise.setInput(0, current_connection)

	noise.parm("attribtype").set("float")
	noise.parm("attribs").set(att_name)
```

The attribute type dropdown needs to be set to "float", we can see these options by opening the parameter interface and checking the menu options for the parameter.

This works great, but right now it shows up when we right click any parameter, so we need to add a condition that allows us to control when this script item shows up in the menu. We can do this in the `PARMmenu.xml` file, using the `expression` option:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<menuDocument>
	<menu>
		<subMenu id="tdp_menu">
		<label>TDP</label>
			<scriptItem id="TDP_rand_inp_att">
				<label>TDP Randomize Input Attribute</label>
				<expression><![CDATA[
import TDPUtils
return TDPUtils.randomize_attribute_condition(kwargs)
				]]></expression>
				<scriptCode><![CDATA[
import TDPUtils
TDPUtils.randomize_input_attribute(kwargs)
				]]> </scriptCode>
			</scriptItem>
		</subMenu>
	</menu>
</menuDocument>
```

As you can see, we need to create a new function (you could also write the small script straight in the menu file, but I don't suggest it). A special note, is that we make our expression `return` what value the function returns, this is needed for the expression to filter correctly.

Let's add our function, and create an if statement to check if the node and parameter name meet our requirements:

```python
def randomize_attribute_condition(kwargs):
	parm = kwargs['parms'][0]
	node = parm.node()

	node_con = node.type().name() == "attribremap"
	parm_con = parm.name() == "inname"

	if node_con and parm_con:
		return 1
	else:
		return 0

def randomize_input_attribute(kwargs):
	parm = kwargs['parms'][0]
	node = parm.node()
	att_name = parm.eval()

	current_connection = node.input(0)

	noise = node.createInputNode(0, "attribnoise::2.0")

	noise.setInput(0, current_connection)

	noise.parm("attribtype").set("float")
	noise.parm("attribs").set(att_name)
```

Here, we create 2 conditions: checking if the `node.type().name()` is "attribremap", and `parm.name()` should be "inname", which is the name for the input attribute parameter.

We use `if node_con and parm_con` to check if both conditions are met. Now, if we reload and our menu script will only show on the parameter we specify.

Now that you've done this, you have know all the steps to create custom menu entries wherever you desire.
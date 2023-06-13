## Introduction

Chest allows you to store objects from anywhere, to keep them around to check equality...   

![Chest overview](./figures/chest-overview.png)
  
### Original repository

[Link to original repository](https://github.com/dupriezt/Chest)

### Install Chest
```smalltalk
Metacello new
    baseline: 'Chest';
    repository: 'github://pharo-spec/Chest';
    load.
```

### Open Chest
Chest is available in the **world menu** of Pharo. It is available as an **entry of the Debug menu**

![Chest in debug menu](./figures/chest-debug-menu.png)

You can also enable it as a debugger extension in the debugging settings of Pharo:

![Chest debugger extension in the Pharo settings](./figures/chest-extension-in-settings.png)

In the debugger, Chest will provide a view as a tree of all chests with the objects inside.

> Maybe we need an image of the tree view here?

### More Details

#### Name (= ID)

Each Chest instance has an ID (String). These IDs are unique. Two chests cannot have the same ID.

#### Default Chest

This is an instance of Chest that can be interacted with in the same way as any other Chest by sending the messages to the `Chest` class.

#### Weak Chests

```Smalltalk
Chest weak
```

is a way to access the class `WeakChest`. This class can be interacted with the same way as you would with the `Chest` class.

`WeakChest` has its own default chest, and can be used to manipulate this default chest or to create new weak chests.

#### Commands in context

##### Store object into chest

If you right-click on a code presenter with an `SpCodeInteractionModel` or `StDebuggerContextInteractionModel` (e.g: playground, debugger etc.), you can evaluate an expression and store the result in the chest of your choice, with the name of your choice:

![Store object context menu, inside the debugger](./figures/chest-store-object-context-menu.png)

![Store object popup](./figures/chest-store-object-popup.png)

![Chest tree view inside the debugger, after having stored an object](./figures/chest-tree-view-after-store.png)

##### Load object from a chest into a playground or a debugger

It's also possible to load objects from a chest into these code presenters:

![Load object context menu, inside the debugger](./figures/chest-load-context-menu.png)

![Load object popup](./figures/chest-load-object-popup.png)

![Usage of the Chest variable inside the debugger, after having loaded the object](./figures/chest-variable-usage-after-load.png)

And then variables can be seen from any other context if it has been loaded in a debugger and these variables can be seen in the debugger inspector:

![Chest variable, in the debugger inspector](./figures/chest-variable-in-debugger-inspector.png)

Chest, as a debugger extension, provides a playground. All bindings between this playground and the debugger selected context are shared. So: all variables defined in this playground are recognized by the debugger and all variables from the debugger's selected context or loaded from Chest into the debugger are recognized by the playground. However, only the variables loaded from Chest (via the load menu entry described above) are displayed in the debugger inspector:

![Variable from Chest playground, not visible in the debugger inspector](./figures/variable-from-chest-playground-are-not-visible-in-debugger-inspector.png)

##### Inject code to access an object inside a chest, in a playground or in the debugger

To make it easier to access the content of a chest in a playground or in the debugger, it is possible to use the **Paste object from chest >** sub-menu.
This sub-menu allows to choose a chest and the key of an object inside the chest.

![Paste object from Chest context menu](./figures/chest-paste-object-from-chest-context-menu.png)

When clicking on a variable name, the necessary code to access the corresponding object is pasted where you had put your cursor in the playground/debugger:

![Injected code for Chest, after having pasted the object](./figures/chest-injected-code-after-paste.png)

This command allows you to access objects from a chest, without relying on your memory of the `Chest` API.

##### Simpler code injection in any spec code presenter

The method described above to access objects in chests implies that your objects need to be stored in a chest with a name that you must remember...

Sometimes, you would like to access objects quickly, without needing to give a name to an object that is stored...

That's why it is possible to copy / paste (=inject code to access) to / from a "Clipboard Chest", just as you would do to copy/paste text to/from the clipboard.

To do that, you just need to select the expression that you want to evaluate and whose results should be stored in the "Clipboard Chest", and then select **Copy object in a clipboard chest** in the context menu:

![Copy object to Chest Clipboard context menu](./figures/chest-copy-object-to-clipboard-context-menu.png)

This will store the result of the expression into the default clipboard chest. Please note, that if you "copy" another object to the clipboard chest, it will replace the previous copied object. Note also that the clipboard chest is a weak chest, so your object inside the clipboard chest can become `nil` if it gets garbage-collected.

In order to inject the code to access the object, you should then select in the context menu: **Paste last object copied to clipboard**:

![Paste last object from Chest Clipboard context menu](./figures/chest-paste-last-object-from-clipboard-context-menu.png)

And the code to access your object is now pasted:

![Injected code for Chest, after having pasted the object from Chest Clipboard](./figures/chest-injected-code-after-paste-from-clipboard.png)

#### API

##### Chest instance API

- `Chest>>#add:` : adds the object in argument to the chest with a default name that is in the form of 'chestName_autoIncrementedNumber'

- `Chest>>#at: ` : gives the object in the chest whose name is the string in argument if it exists, else `KeyNotFound`

- `Chest>>#at:put:` : adds the object in second argument to the chest with the name in first argument if no other object is already named so, else `ChestKeyAlreadyInUseError`

- `Chest>>#contents` : gives a copy of a chest's contents as a dictionary

- `Chest>>#remove:` : removes the object in argument from a chest if it is there, else `KeyNotFound`

- `Chest>>#removeObjectNamed:` : removes the object whose name is in argument if it is there, else `ObjectNotInChestError`

The 6 messages above can be sent to `Chest class` unlike the ones below:

- `Chest>>#empty`: empties a chest.

- `Chest>>#remove` : completely deletes this chest

- `Chest>>#name` : gives this chest's name

- `Chest>>#name:` : renames this chest as the string in argument if no other chest is already named so, else `ChestKeyAlreadyInUseError`

- `Chest>>#renameObject:into:` : renames the object (first argument) in a chest as the string in second argument; if the object is in the chest else `ObjectNotInChestError` and if no other object is already named so else `ChestKeyAlreadyInUseError`

- `Chest>>#inspectAt` : inspect in a chest the object whose name is in argument

##### `Chest class` API

- `Chest class>>#allChests` : gives an ordered collection that contains all chests

- `Chest class>>#chestDictionary` : gives all chests with their names as a dictionary

- `Chest class>>#named:` : gives the chest that is named as the string in argument

- `Chest class>>#defaultInstance` ( or `Chest class>>#default`) : gives the default chest that is used when you use `Chest` API on `Chest class`

- `Chest class>>#inChest:at:put:` : puts an object with a given name in a chest of a given name that is created it it doesn;t exist yet.

- `Chest class>>#announcer` : helper that gives the unique instance of `ChestAnnouncer`

- `Chest class>>#unsubscribe:` : helper that unsubscribes its argument from the unique instance of `ChestAnnouncer`

- `Chest class>>#weak` : returns `WeakChest class`. You can use the same API on this class as you would on `Chest class`, in order to create or access chests that hold weak references to objects.

###### How to create instances

- `Chest class>>#new` : creates a chest with a default name that is in the form of 'Chest_autoIncrementedNumber'

- `Chest class>>#newNamed:` : creates a chest with the name given in parameter if no other chest is already named so, else `ChestKeyAlreadyInUseError`

#### Example

```smalltalk
	Chest new. "its name is 'Chest_1' if no other chest have been created before"
	Chest new. "its name is 'Chest_2' if no other chest have been created before"
	Chest newNamed: 'toto'. "its name is 'toto'"
	Chest newNamed: 'toto'. "ChestKeyAlreadyInUseError as a chest named 'toto' already exists"
	
	(Chest named: 'toto') add: 42.
	"42 has the key 'toto_1' in the chest named 'toto'"
	
	(Chest named: 'toto') at: 'toto' put: 42.
	(Chest named: 'toto') at: 'toto'. "returns 42"
	"42 has the key 'toto' and not 'toto_1' anymore as an object has a unique key in a chest."
	
	(Chest named: 'toto') at: 'toto' put: 72.
	"72 is not put in the chest: a ChestKeyAlreadyInUseError is raised as 42 already has the key 'toto'"
	
	(Chest named: 'toto') renameObject: 42 into: 'tata' .
	"42 has now the key 'tata' in the chest named 'toto'"
	
	(Chest named: 'toto') removeObjectNamed: 'tata'.
	"42 is removed as it had the key 'tata'"
	
	(Chest named: 'toto') at: 'toto' put: 72.
	(Chest named: 'toto') remove: 72. 
	"72 is not in the chest anymore"
	
	(Chest named: 'toto') remove: 72. "KeyNotFound"
	(Chest named: 'toto') renameObject: 72 into: 'toto'. "ObjectNotInChestError"
	(Chest named: 'toto') removeObjectNamed: 'toto'. "ObjectNotInChestError"
	
	(Chest named: 'toto') 	at: 'toto' put: 42;
									at: 'tata' put: 72;
									renameObject: 42 into: 'tata'. "ChestKeyAlreadyInUseError"
	
	(Chest named: 'toto') contents at: 'tata' "returns 72"
							
	Chest defaultInstance	name: 'toto' "ChestKeyAlreadyInUseError"					
	
	(Chest named: 'toto') remove.
	"deletes the chest named 'toto', and all objects inside"
	
	Chest named: 'toto'. "KeyNotFound"
	Chest defaultInstance name: 'toto'. "the default chest is now named 'toto'"
	
	Chest defaultInstance remvove. "the default chest is still there as it can't be removed"
	
	Chest add: 42. "adds 42 to the default chest"
	
	Chest inChest: 'titi' at: 'tata' put: 42. "creates the chest named 'titi' as it doesn't exist, and put 42 in it with the name 'tata' "
	
	Chest inChest: 'titi' at: 'toto' put: 43. "puts 43 in the chest named 'titi' with the name 'toto'"
	
	Chest weak inChest: 'wtiti' at: 'tata' put: 42 "puts 42 in the weak chest named 'wtiti' with the name 'tata'"
```
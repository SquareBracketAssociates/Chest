## API Description

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
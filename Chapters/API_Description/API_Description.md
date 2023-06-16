## API Description

In this chapter, we detail the API of Chest and give examples of its usage. 

### Chest class-side API

In this section, we describe the API that can be used on the class `Chest`.

#### How to create instances

- `Chest class>>#new`
	creates a chest with a default name that is in the form of `Chest_autoIncrementedNumber`.

	In the examples below, if no other chest has been created before, the names of the created chests are respectively "Chest_1" and "Chest_2": 

	```smalltalk
	Chest new. "Chest_1"
	Chest new. "Chest_2"
	```

- `Chest class>>#newNamed:`
	creates a chest with the name given in parameter if no other chest is already named so, else an exception `ChestKeyAlreadyInUseError` is raised.

	For example, if no other chest is already called "toto", the piece of code below creates a chest that is named as "toto":

	```smalltalk
	Chest newNamed: 'toto'. "its name is 'toto'"
	```

	On the contrary, if another chest is already called "toto", the same piece of code would raise a `ChestKeyAlreadyInUseError` because two chests cannot have the same name:

	```smalltalk
	Chest newNamed: 'toto'. "ChestKeyAlreadyInUseError as a chest named 'toto' already exists"
	```

#### Accessors

- `Chest class>>#allChests` 
	returns an ordered collection that contains all chest instances.

	If we suppose that there are no other chests than the ones created above, the piece of code returns a collection with two chests: the default chest and the chest named "toto":

	```smalltalk
	Chest allChests "{DefaultChest. totoChest}"
	```

- `Chest class>>#chestDictionary` 
	returns a dictionary containing all chests with their name as key.

	If we suppose that there are no other chests than the ones created above, the piece of code returns a dictionary with two chests: the default chest, with 'Default' as a key, and the chest named "toto", with 'toto' as a key:

	```smalltalk
	Chest chestDictionary "{'Default' -> DefaultChest. 'toto' -> totoChest}"
	```

- `Chest class>>#named:`
	returns the chest that is named as the string in argument if it exists, else raises an exception `KeyNotFound`.

	The expression below can be used to get the chest named 'toto', if it exists:

	```smalltalk
	Chest named: 'toto' "chest named 'toto'"
	```

	However, no chest is named 'titi', the expression below would raise a `KeyNotFound`:

	```smalltalk
	Chest named: 'titi' "KeyNotFound"
	```

- `Chest class>>#defaultInstance` ( or `Chest class>>#default`).
	You can use most of Chest's instance-side API on the class `Chest` itself. These methods returns the default chest that is used when you use `Chest`'s instance-side API directly on `Chest class`.
	If the default chest doesn't already exist, calling these methods lazily create it.

	For example, the expression below returns `true`:

	```smalltalk
	Chest defaultInstance == Chest default "true"
	 ```

- `Chest class>>#announcer`
	helper method that returns the unique instance of `ChestAnnouncer`, which propagates changes related to chests to any subscriber.

	The example belows subscribes `self` to the `ChestAnnouncer` singleton to 3 different events; when a new chest chest has been created, when the content of a chest has changed and when a chest has been removed:

	```smalltalk
	Chest announcer weak when: ChestCreated send: #eventNewChest: to: self;
						 when: ChestUpdated send: #eventContentOfChestUpdated: to: self.
						 when: ChestRemoved send: #eventChestRemoved: to: self.
	```

	When the event happens, the methods that are called (in the example: `#eventNewChest:`, `#eventContentOfChestUpdated:` and `#eventChestRemoved:`) take the related event as an argument.

- `Chest class>>#weak`
	returns the class `WeakChest`, subclass of `Chest`. You can use the same API on this class as you would on `Chest class`, in order to create or access chests that hold weak references to objects. This means, that storing your objects in a weak chest doesn't prevent them from being garbage-collected.

	For example, the expression below creates a new weak chest named as "wtiti", if it doesn't already exist:

	```smalltalk
	Chest weak newNamed: 'wtiti' "weak chest named as 'wttiti'"
	```

#### How to perform actions

- `Chest class>>#inChest:at:put:`
	puts the object in third argument with the name given in second argument in a chest named as first argument. This chest is created if it doesn't exist yet.

- `Chest class>>#unsubscribe:`
	helper method that unsubscribes its argument from the unique instance of `ChestAnnouncer`.


### Chest instance-side API

- `Chest>>#add:` 
	adds the object in argument to the  receiver chest, with a default name that is in the form of `chestName_autoIncrementedNumber`

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

#### Example

```smalltalk
	
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
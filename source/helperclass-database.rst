Dapp State Database
===================

The contractvm framework offer a database interface for saving internal state of a dapp. A fresh empty and private
state database is created for every dapp, and it is exploitable in the Core of your dapp. The database is a
simple key-value database, where we can store array or complex dict object (similar to a json object).

For instance, we can see a basic usage of database in the **dapp.Core** of the FirstDapp tutorial:

.. code-block:: python

	class BlockStoreCore (dapp.Core):
		def __init__ (self, chain, database):
			super (BlockStoreCore, self).__init__ (chain, database)

		def set (self, key, value):
			if self.database.exists (key):
				return
			else:
				self.database.set (key, value)

		def get (self, key):
			if not self.database.exists (key):
				return None
			else:
				return self.database.get (key)



The Database class is more complex than a simple get/set.

Basic usage
-----------

.. function:: database.get(key)

	Get the value given its key.

	:param key: key to query
	:type key: string
	:rtype: array or dict


.. function:: database.set(key, value)

	Set a key with a value.
	
	:param key: key to set
	:type key: string
	:param value: value to set
	:type value: array or dict


.. function:: database.exists(key)

	Check if a key exists: return true if key exist.
	
	:param key: key to check
	:type key: string
	:rtype: boolean



.. function:: database.delete(key)

	Delete a key.
	
	:param key: key to delete
	:type key: string



Integers
--------

.. function:: database.intinc(key)

	If key is set and contains a number, increment the value.
	
	:param key: key to increment
	:type key: string


.. function:: database.decinc(key)

	If key is set and contains a number, decrement the value.
	
	:param key: key to decrement
	:type key: string


Lists
-----

.. function:: database.listappend(key, item)

	If key is an array, append item as array item.

	:param key: array key
	:type key: string
	:param item: item to append
	:type item: dict or array


.. function:: database.listremove(key, item)

	If key is an array, remove the element item from the array

	:param key: array key
	:type key: string
	:param item: item to remove
	:type item: dict or array



Initialization
--------------

.. function:: database.init(key, value)

	If not exists, initialize key with value.

	:param key: array key
	:type key: string
	:param value: initialization value
	:type value: dict or array


.. function:: database.getinit(key, value)

	If not exists, initialize key with value. Return the previous value or the new value

	:param key: array key
	:type key: string
	:param value: initialization value
	:type value: dict or array
	:rtype: dict or array


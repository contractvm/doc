Dapp
====

We start from editing the dapp part that runs in nodes, located at *myfirstdapp/dapp/myfirstdapp.py*.

Protocol and messages
.....................

First, we define the protocol and the supported messages of the dapp through set 
of constants containing the code for each type of message and the dapp code. 

.. code-block:: python

	class BlockStoreProto:
		DAPP_CODE = [0x05, 0x66]
		METHOD_SET = 0x01
		METHOD_LIST = [METHOD_SET]


Then we extend the **Message** class, by defining a constructor
for the set message, and by overriding the function *toJSON()* for
the serialization of message data.

.. code-block:: python

	class BlockStoreMessage (message.Message):
		def set (key, value):
			m = BlockStoreMessage ()
			m.Key = key
			m.Value = value
			m.DappCode = proto.BlockStoreProto.DAPP_CODE
			m.Method = proto.BlockStoreProto.METHOD_SET
			return m

		def toJSON (self):
			data = super (BlockStoreMessage, self).toJSON ()

			if self.Method == proto.BlockStoreProto.METHOD_SET:
				data['key'] = self.Key
				data['value'] = self.Value
			else:
				return None

			return data


Core
.........

The next step is to write the core of our dapp: this is done in the next code-block by
extending the class **dapp.Core**. In this class we define all the methods that
interact with the dapp state, including query and pair insertion. We define a
function to obtain a value given its key, and another one to set a new key-value
pair. We save key-value pairs in the internal database which is automatically
created by the framework to store the state of a dapp.


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



API
...

The services offered by the dapp are exposed to client applications as APIs.
These APIs are implemented in the next code-block, where we extend the class **dapp.API**,
and we create a dict object which contains new API calls. Then, we
write our two APIs:

- *set (key,value)*: creates a set message with a new key-value pair, and returns message broadcasting information;
- *get (key)*: gets a value for a given key, by invoking the Core.get method.


.. code-block:: python

	class BlockStoreAPI (dapp.API):
		def __init__ (self, core, dht, api):
			self.api = api
			rpcmethods = {}

			rpcmethods["get"] = {
				"call": self.method_get,
				"help": {"args": ["key"], "return": {}}
			}

			rpcmethods["set"] = {
				"call": self.method_set,
				"help": {"args": ["key", "value"], "return": {}}
			}

			errors = { 
				'KEY_ALREADY_SET': {'code': -2, 'message': 'Key already set'}, 
				'KEY_IS_NOT_SET': {'code': -3, 'message': 'Key is not set'} 
			}

			super (BlockStoreAPI, self).__init__(core, dht, rpcmethods, errors)


		def method_get (self, key):
			v = self.core.get (key)
			if v == None:
				return self.createErrorResponse ('KEY_IS_NOT_SET')
			else:
				return v
		
		def method_set (self, key, value):
			if self.core.get (key) != None:
				return self.createErrorResponse ('KEY_ALREADY_SET')
		
			msg = message.BlockStoreMessage.set (key, value)
			return self.createTransactionResponse (msg)




Mixing all classes
..................

Finally, we bind all the classes created so far by extending the **dapp.Dapp** class. We use the method *handleMessage* to tell the framework daemon 
how to handle each message.


.. code-block:: python

	class myfirstdapp (dapp.Dapp):
		def __init__ (self, chain, db, dht, apiMaster):
			self.core = core.BlockStoreCore (chain, db)
			apiprov = api.BlockStoreAPI (self.core, dht, apiMaster)
			super (myfirstdapp, self).__init__(proto.BlockStoreProto.DAPP_CODE, 
					proto.BlockStoreProto.METHOD_LIST, chain, db, dht, apiprov)

		def handleMessage (self, m):
			if m.Method == proto.BlockStoreProto.METHOD_SET:
				logger.pluginfo ('Found new message %s: set %s', m.Hash, m.Data['key'])
				self.core.set (m.Data['key'], m.Data['value'])


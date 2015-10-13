Writing your first Dapp
-----------------------

This section is a brief tutorial for dapp developers. We illustrate how to write a
dapp by commenting the source code of a decentralized
key-value storage service. The full source code of this dapp is available at 
https://github.com/contractvm/cvm-dapp-blockstore.
The key-value storage dapp offers two APIs: set(key,value) and get(key).
A set request saves a new immutable key-value pair, while get retrieves a pre-
viously set value. Since set changes the state of the dapp, this API requires the
client to publish a suitable message in the blockchain. Nodes scan the blockchain
for new messages, and when they find a set request, they save the new key-value
pair in their local database. Instead, executing a get does not require to publish
any message, because nodes can handle this request internally.
The dapp is split in two main parts: the first is the dapp which runs in nodes,
while the second implements the library that client applications use
to interact with the dapp. Note that, while we have chosen Python for develop-
ing this dapp, our framework supports arbitrary programming languages. The
mechanism used to this purpose is standard: the programmer codes the core features 
of dapp in her preferred language, and the framework uses them through
the foreign function interface.

We start by creating an empty dapp with the empty template:

.. code-block:: bash
	
	dappman -w

.. code-block:: plain
	
	Dapp name: myfirstdapp
	Description: This is my first dapp
	Authors (comma separated): Davide Gessa
	Select a template:
		 0 . empty (https://github.com/contractvm/cvm-dapp-empty)
		 1 . blockstore (https://github.com/contractvm/cvm-dapp-blockstore)
		 2 . helloworld (https://github.com/contractvm/cvm-dapp-helloworld)
		 3 . fifomom (https://github.com/contractvm/cvm-dapp-fifomom)
		 4 . cotst (https://github.com/contractvm/cvm-dapp-cotst)
	Template: 0
	Creating directory for dapp: myfirstdapp
	Downloading template: empty
	Extracting template
	Setting up directories
	String replace for dapp name
	Creating manifest.json
	Dapp myfirstdapp sucessfully created

This command will create a new dapp tree:

.. code-block:: bash

	myfirstdapp/
		requirements.txt
		setup.py
		README.md
		manifest.json
		dapp/
			myfirstdapp.py
			__init__.py
		library/
			myfirstdapp/
				__init__.py
				EmptyManager.py
		samples/
			config.py
			data
		

Now every time we want to install the updated dapp, we type:

.. code-block:: bash
	
	dappman -i /path/of/your/dapp/myfirstdapp



Dapp
====

We start from editing the dapp part that runs in nodes, located at myfirstdapp/dapp/myfirstdapp.py.

Protocol and messages
.....................

First, we define the protocol and the supported messages of the dapp through set 
of constants containing the code for each type of message and the dapp code. 

.. code-block:: python

	class BlockStoreProto:
		DAPP_CODE = 0x08
		METHOD_SET = 0x01
		METHOD_LIST = [METHOD_SET]


Then we extend the Message class, by defining a constructor
for the set message, and by overriding the function toJSON() for
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
extending the class dapp.Core. In this class we define all the methods that
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
These APIs are implemented in the next code-block, where we extend the class dapp.API,
and we create a dict object which contains new API calls. Then, we
write our two APIs:

- set (key,value) : creates a set message with a new key-value pair, and returns message broadcasting information;
- get (key) : gets a value for a given key, by invoking the Core.get method.


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
			[datahash, outscript, tempid] = msg.toOutputScript (self.dht)
			r = { 
				"outscript": outscript, 
				"datahash": datahash, 
				"tempid": tempid, 
				"fee": proto.Protocol.estimateFee (self.core.getChainCode (), 
									100 * len (value)) 
			}
			return r




Mixing all classes
..................

Finally, we bind all the classes created so far. We use the method handleMessage to tell the framework daemon 
how to handle each message.


.. code-block:: python

	class blockstore (dapp.Dapp):
		def __init__ (self, chain, db, dht, apiMaster):
			self.core = core.BlockStoreCore (chain, db)
			apiprov = api.BlockStoreAPI (self.core, dht, apiMaster)
			super (blockstore, self).__init__(proto.BlockStoreProto.DAPP_CODE, 
					proto.BlockStoreProto.METHOD_LIST, chain, db, dht, apiprov)

		def handleMessage (self, m):
			if m.Method == proto.BlockStoreProto.METHOD_SET:
				logger.pluginfo ('Found new message %s: set %s', m.Hash, m.Data['key'])
				self.core.set (m.Data['key'], m.Data['value'])


Client library
==============


Now we edit the dapp part used directly by clients, located at myfirstdapp/library/. The empty template
has a file called myfirstdapp/library/EmptyManager.py, but we can rename it with a better name, like "MyFirstManager.py".


In our library, we define a module that binds the API calls described after, which will be used to write client applications. We
do this by extending the DappManager. This class includes the services of our
dapp, by binding the API calls myfirstdapp.get and myfirstdapp.set. The method set only creates
and broadcasts a new message containing the given key-value pair; the method
get performs a consensus query to nodes, and returns the resulting value.


.. code-block:: python

	class MyFirstManager (DappManager.DappManager):
		def __init__ (self, consensusManager, wallet = None):
			super (MyFirstManager, self).__init__(consensusManager, wallet)

		def set (self, key, value):
			cid = self._produce_transaction ('myfirstdapp.set', [key, value])
			return cid
	
		def get (self, key):
			r = self.consensusManager.jsonConsensusCall ('myfirstdapp.get', [key])
			return r['result']


Example usage
=============

We now exploit the created dapp for writing a client application. We first create a ConsensusManager, 
and we initialize it with a static set of nodes (at the moment we can use the our local contractvmd instance). 

Then we create a Wallet object, by using a local instance of
bitcoin-core with private keys saved in the file app.wallet. 

Next we create a MyFirstManager, by using the ConsensusManager and Wallet objects
created before. The script asks the user for a key-value pair, and
it publishes it to the framework. Then, the script asks the
user for a key, and then queries and returns the associated value (if any).

.. code-block:: python

	from libcontractvm import Wallet, WalletNode, ConsensusManager
	from myfirstdapp import MyFirstManager
	import sys
	import config

	consMan = ConsensusManager.ConsensusManager ()
	consMan.addNode ("http://127.0.0.1:8181")

	wallet=WalletNode.WalletNode (chain='XLT', url=config.WALLET_NODE_URL, 
					wallet_file='data/test_xltnode_a.wallet')
			
	bsMan = MyFirstManager.MyFirstManager (consMan, wallet=wallet)

	def set_key ():
		ykey = input ('Insert a key to set: ')
		yvalue = input ('Insert a value to set: ')
		bsMan.set (ykey, yvalue)
	
	def get_key ():
		ykey = input ('Insert a key to get: ')
		value = bsMan.get (ykey) 
		print (ykey,'=',value)


	if __name__ == "__main__":
		if len (sys.argv) > 1:
			if sys.argv[1] == 'set':
				set_key ()
				sys.exit (0)
		
			elif sys.argv[1] == 'get':
				get_key ()
				sys.exit (0)
		
		print ('usage:', sys.argv[0], 'get|set')

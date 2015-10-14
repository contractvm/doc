Writing your first Dapp
-----------------------

This section is a brief tutorial for dapp developers. We illustrate how to
write a dapp by commenting the source code of a decentralized
key-value storage service. The full source code of this dapp is available at 
https://github.com/contractvm/cvm-dapp-blockstore.
The key-value storage dapp offers two APIs: **set(key, value)** and **get(key)**.
A *set* request saves a new immutable key-value pair, while *get* retrieves a
previously set value. Since set changes the state of the dapp, this API
requires the client to publish a suitable message in the blockchain.
Nodes scan the blockchain for new messages, and when they find a *set*
request, they save the new key-value pair in their local database.
Instead, executing a *get* does not require to publish
any message, because nodes can handle this request internally.

The dapp is split in two main parts: the first is the dapp which runs in nodes,
while the second implements the library that client applications use
to interact with the dapp. Note that, while we have chosen Python for developing 
this dapp, our framework supports arbitrary programming languages. The
mechanism used to this purpose is standard: the programmer codes the core
features  of dapp in her preferred language, and the framework uses them
through the foreign function interface.

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



Now we can write the parts of the dapp:

.. toctree::
	:maxdepth: 5

	tutorial-firstdapp-dapp
	tutorial-firstdapp-lib
	tutorial-firstdapp-example

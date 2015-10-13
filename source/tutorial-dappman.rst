Dappman: the dapp manager
-------------------------

Dappman is a command line tool which provides package manager features for contractvm dapps:

- Dapp installation from local or git repository
- Dapp updating and removal
- Dapp listing from a public catalog
- Dapp creation wizard 

After each update (dapp installation/update/removal), dappman will automatically restart the
contractvmd daemon with the new configuration.


Listing dapps
=============

The contractvm infrastructure provide a repository (https://github.com/contractvm/dapp-list) that 
allows dapp developer to include their dapp in a public catalog, by performing a pull request.

Catalog and installed dapps are available with the "list" command:

.. code-block:: bash

	dappman -l

The output of that command shows the list of installed, enabled and catalog dapps.


.. code-block:: plain

	Installed dapps:
		 helloworld
	Enabled dapps:
		 helloworld
	Available:
		 empty - decentralized empty template
		 blockstore - decentralized key-value storage
		 helloworld - decentralized helloworld
		 fifomom - decentralized fifo message-oriented middleware
		 cotst - decentralized contract-oriented middleware based on timed session types


Installing a dapp
=================

Installation of dapps is easy; first you need the catalog dapp name, or the dapp git-url, then you can use the "install" command. 
For example:

.. code-block:: bash
	
	dappman -i blockstore

Or the equivalent:

.. code-block:: bash

	dappman -i https://github.com/contractvm/cvm-dapp-blockstore.git


For developing purposes, it is also possible to install a dapp directly from the file system:

.. code-block:: bash

	dappman -i /home/dakk/Repositories/dapp-blockstore/


All these variants are equivalent; dappman retrieves the dapp source, installing the client library and the dapp in the dapp directory.
If the dapp is installed via git (catalog or git-url), then you can update an installed dapp by typing:

.. code-block:: bash
	
	dappman -u blockstore



Removing a dapp
===============

The removal of a dapp is similar to installation:

.. code-block:: bash

	dappman -r blockstore

Note that this command remove only the source code of the dapp, preserving the current dapp state. You can also remove the dapp state by using:

.. code-block:: bash

	dappman -c blockstore



Creating a new dapp
===================

Using dappman, you can also create a new dapp through the dappman creation wizard. A new dapp is created starting from a template: you can use 
every available dapp as a template. For those who want only the dapp skeleton, an empty template called "empty" is available.

.. code-block:: bash

	dappman -w

Dappman will ask some informations about your new dapp:

.. code-block:: plain

	Dapp name: testdapp
	Description: My first test dapp
	Authors (comma separated): Davide Gessa
	Select a template:
		 0 . empty (https://github.com/contractvm/cvm-dapp-empty)
		 1 . blockstore (https://github.com/contractvm/cvm-dapp-blockstore)
		 2 . helloworld (https://github.com/contractvm/cvm-dapp-helloworld)
		 3 . fifomom (https://github.com/contractvm/cvm-dapp-fifomom)
		 4 . cotst (https://github.com/contractvm/cvm-dapp-cotst)
	Template: 0
	Creating directory for dapp: testdapp
	Downloading template: empty
	Extracting template
	Setting up directories
	String replace for dapp name
	Creating manifest.json
	Dapp testdapp sucessfully created
	You can now install your local dapp by typing: dappman -i /home/dakk/testdapp


After the creation wizard, in your current directory appears a new directory called as your new dapp, and you can directly install it.
Further informations about dapp creation are provided in the next tutorial. 






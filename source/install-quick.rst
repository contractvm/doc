Quick install
.............

At the moment, contractvm has some requirements and it is tested only on linux systems; requirements are:

- python3 (3.4 or greater)
- python3-pip 
- curl
- git

You can easily install them in ubuntu linux by typing:

.. code-block:: bash

	sudo apt-get install python3 python3-pip curl git

	
Now use pip3 for installing contractvmd and its client library as follow:

.. code-block:: bash
		
	sudo pip3 install contractvm
	sudo pip3 install libcontractvm

.. download and execute the quick installation script provided in contractvmd:
..	curl https://raw.githubusercontent.com/contractvm/contractvmd/master/quickinstall.sh | sh

After the script execution, the contractvmd daemon, libraries and dapp manager should be installed.

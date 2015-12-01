Quick install
.............

At the moment, contractvm has some requirements and it is tested only on linux systems; requirements are:

- python3 (3.4 or greater)
- python3-pip 
- curl
- git
- libxml2

You can easily install them in ubuntu linux by typing:

.. code-block:: bash

	curl https://raw.githubusercontent.com/contractvm/contractvmd/master/quickinstall.sh | sh



Other distribution / OS
.......................

You can install contractvm in other distribution or operating systems using pip3:

.. code-block:: bash
		
	sudo pip3 install --upgrade contractvm
	sudo pip3 install --upgrade libcontractvm

.. download and execute the quick installation script provided in contractvmd:
..	curl https://raw.githubusercontent.com/contractvm/contractvmd/master/quickinstall.sh | sh

After the script execution, the contractvmd daemon, libraries and dapp manager should be installed.

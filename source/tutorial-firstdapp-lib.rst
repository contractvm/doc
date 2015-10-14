Client library
==============


Now we edit the dapp part used directly by clients, located at *myfirstdapp/library/*. The empty template
has a file called *myfirstdapp/library/EmptyManager.py*, but we can rename it with a better name, like *MyFirstManager.py*.


In our library, we define a module that binds the API calls described after, which will be used to write client applications. We
do this by extending the **licontractvm.DappManager**. This class includes the services of our
dapp, by binding the API calls *myfirstdapp.get* and *myfirstdapp.set*. The method set only creates
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


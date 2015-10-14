Example usage
=============

We now exploit the created dapp for writing a client application. We first create a **ConsensusManager**, 
and we initialize it with a static set of nodes (at the moment we can use the our local contractvmd instance). 

Then we create a **Wallet** object, by using a local instance of
*bitcoin-core* with private keys saved in the file *app.wallet*. 

Next we create a **MyFirstManager**, by using the *ConsensusManager* and *Wallet* objects
created before. The script asks the user for a key-value pair, and
it publishes it to the framework. Then, the script asks the
user for a key, and then queries and returns the associated value (if any).

.. code-block:: python

	from libcontractvm import Wallet, WalletNode, ConsensusManager
	from myfirstdapp import MyFirstManager
	import sys
	import config

	consMan = ConsensusManager.ConsensusManager ()
	consMan.bootstrap ("http://127.0.0.1:8181")

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

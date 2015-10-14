Deploy a test network of nodes
..............................

You can also run a local complex test network of peers easily; for instance, if you want to create a network with 4 nodes:


.. code-block:: bash

	# Start the first node
	contractvmd --data=~/test-node-data1 --port=5010 --api-port=8010

	# Start the second node, bootstrap from first
	contractvmd --data=~/test-node-data2 --port=5011 --api-port=8011 --seed=127.0.0.1:5010

	# Start the third node, bootstrap from second
	contractvmd --data=~/test-node-data3 --port=5012 --api-port=8012 --seed=127.0.0.1:5011

	# Start the fourth node, bootstrap from first and third
	contractvmd --data=~/test-node-data4 --port=5013 --api-port=8013 --seed=127.0.0.1:5010,127.0.0.1:5012


Now we have a local network of 4 nodes, accessible by clients from these host:port:

- localhost:8010
- localhost:8011
- localhost:8012
- localhost:8013

If a client application need to access to the local test network, you can fill one of these node pair; the bootstrap mechanism will 
automatically find the whole network of nodes.




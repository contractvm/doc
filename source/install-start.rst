Daemon start
............

To start the contractvm daemon for the first time, type:

	
.. code-block:: bash

	contractvmd


The contractvm daemon provides a set of useful command line options; you can specify another daemon and api port, use a custom
data directory, bootstrap from a seed list, and other technical things. By default, contractvmd will start in Bitcoin testnet
chain (XTN), using bitpeer.py as backend.

There are also 2 daemon commands, for sending instructions to a running instance.

.. code-block:: plain

	Usage: /usr/local/bin/contractvmd [OPTIONS]

	Mandatory arguments:
		-h,--help			display this help
		-V,--version			display the software version
		-v,--verbose=n			set verbosity level to n=[1-5] (default: 5)
		-D,--data=path			specify a custom data directory path (default: /home/dakk/.contractvm)
		-d,--daemon			run the software as daemon
		-c,--chain=chainname		block-chain ['XTN', 'DOGE', 'BTC', 'XLT', 'LTC']
		-b,--backend=protocol		backend protocol ['node', 'rpc', 'chainsoapi']
		-p,--port=port			dht port
		-a,--api=bool			disable or enable api framework
		--api-port=port			set an api port
		-s,--seed=host:port,[host:port]	set a contractvm seed peer list
		--discard-old-blocks		discard old blocks

	Daemon commands:
		--restart			restart the contractvmd instance
		--stop				stop the contractvmd instance


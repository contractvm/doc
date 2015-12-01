Introduction
------------

Contractvm is a general-purpose decentralized framework based on blockchain. The framework is 
extensible through a pluggable infrastructure that allows to implement arbitrary decentralized 
applications (dapps) in an easy way, providing the necessary abstraction layers. 

Dapps are executed in a network of contractvm nodes where every node process dapp 
interactions, updating and keeping a local computation state of each dapp. 
A node is like an oracle, that can be queried by clients for retrieving the state of running dapps. 

.. image:: http://contractvm.github.io/media/cvm-decentr.png
    :width: 200px
    :align: center


Dapp messages and blockchain
............................

Dapp interactions (called messages) are stored using a data structure that combines a 
blockchain with a distributed hash table; message data are saved inside the DHT, exploiting 
the blockchain only for cryptographic proof. Contractvm can use any of the existing blockchains 
that follow the bitcoin-core protocol (bitcoin, litecoin, dogecoin and more than 500 other). 

.. image:: http://contractvm.github.io/media/cvm-data-model.png
	:width: 500px
	:align: center


The blockchain protocol is implemented inside the contractvmd daemon through the bitpeer.py library,
so it doesn't require an external blockchain daemon. In addition, the contractvmd daemon saves locally
only block headers and transactions that contains a contractvm message, mitigating the blockchain 
storage footprint.


Client-side consensus
.....................

Correctness of computations is guaranteed by a client-side consensus mechanism: when a 
client queries a dapp, the query is replicated and sent to a set of distinct nodes in a 
trasparent manner; the actual result is obtained by taking the majority of the received 
answers. Moreover, this design choice gives to nodes the ability to choose which dapps handle,
providing a true separation between decentralized applications.

.. image:: http://contractvm.github.io/media/cvm-consensus.png
	:width: 350px
	:align: center


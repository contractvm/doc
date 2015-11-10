Using Java
==========

Our suggestion for embedding Java code in your dapp, is to use rubicon library.
You can install it by adding the dependency "rubicon-java" in the requirements.txt file.

Then you can use any Java class in your dapp source code as follow:

.. code-block:: python

	from rubicon.java import JavaClass

	URL = JavaClass("java/net/URL")
	url = URL("http://pybee.org")
	print url.getHost()

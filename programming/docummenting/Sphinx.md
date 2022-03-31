#documentation 

```bash
yay -S python-sphinx
sphinx-quick-start
shpinx-build -b html source_dir build_dir
```

```rst
# index.rst
Doc of test
===========

.. toctree::
	./myclass.rst
	./other.rst
```

```rst
MyClass
=======

..autoclass:: mypack.models.MyClass
	:members:
```

## Docstring
```python
class MyClass:
	"""This class does this and that

	You can do X by changing this code:

	.. code-block:: python

		def my_func():
			do_this()
	"""

	def __init__(self):
		"""Initiate the class instance

		This will then call :func:`~mypack.models.MyClass.do_that`.
		"""
		self.a = 0
		self.b = 0

		self.do_that()

	def do_that(self):
		"""Does nothing"""
		pass

	def my_func(self, n: int, text: str) -> list:
		"""Returns a list filled with ``n`` times ``text``

		:param int n: The number of elements wanted
		:param str text: The text to fill the elements
		:returns: The list filled
		:rtype: list
		"""
		return [text for x in range(n)]
```

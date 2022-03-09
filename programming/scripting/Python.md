#python #programming #async #websocket

# Websocket
https://websockets.readthedocs.io/en/stable/
https://docs.python.org/3/library/asyncio-task.html#awaitables

```python
async def a():
	# some blocking code
	return 0x45

task = asyncio.create_task(a())
for x in range(5):
	await task
```


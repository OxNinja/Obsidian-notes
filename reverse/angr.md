#reverse 

A cool [[Reverse]] tool

```bash
pip install angr
```

https://docs.angr.io/examples

## Simulation
Ca permet facilement de fuzz les chemins de maniere conditionnelle en filtrant sur un string par exemple

https://docs.angr.io/core-concepts/pathgroups#stepping

```python
import angr

proj = Project("./binary")
state = proj.factory.entry_state()
simgr = proj.factory.simgr(state)

while len(simgr.active) == 1:
	simgr.step()

simgr.run()
print(simgr)

simgr.move(from_stash="deadended", to_stash="ok",
	filter_func=lambda x: b'Congrats' in x.posix.dumps(1))

print(simgr)
```

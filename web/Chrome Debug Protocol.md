#web #chrome #debug #adb
CDP permet de piloter à distance un navigateur [[Chrome]] ou tout navigateur implémentant chromium ([[Firefox]], [[Chromium]]...)
https://developer.chrome.com/docs/extensions/reference/debugger/
https://developer.chrome.com/docs/devtools/remote-debugging/
https://developer.chrome.com/docs/devtools/remote-debugging/local-server/

* activer le mode developpeur sur un device
* activer le debug usb
* connecter le device en usb sur la machine
* `android-tools` pour avoir `adb`
* `adb forward --list`
* `adb forward tcp:9022 localabstract:chrome_devtools_remote`
* `adb forward --delete tcp:9022`

## ppadb
```python
from ppadb.client import Client

c = Client(host="localhost", port=5037)
d = c.devices()[0]

# forwarding local port 9000 to chrome devtools remote
d.forward(local="tcp:9000", remote="localabstract:chrome_devtools_remote")
```

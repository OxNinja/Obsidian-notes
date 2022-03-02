#docker #container #security 
https://docs.docker.com/storage/storagedriver/device-mapper-driver/
to use host USB devices in container without `--privileged` 
`devicemapper`

## Save Docker image
`docker save -o my-image.tar my-image:tag`
## Load Docker image
`docker load -i my-image.tar`
## Use Android device
Without `--privileged` 🔥
* Host
	* `adb kill-server` to free the device if already connected
	* `lsusb` -> get device mapping
	* check in `/dev/bus/usb/XXX/YYY` your device mapping (bus, device)
	* `docker run --device /dev/bus/usb/XXX/YYY image`
* Container
	* `adb start-server`
	* `adb devices` should list the device

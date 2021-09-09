# FreeRadius over alpine:edge with EAP-TLS (freeradius-alpine-raw)

## Steps to build container

### Load certificates
Copy the certificates you want into *certs* folder, and edit the Dockerfile **ENV** based on what was loaded. Right now certs available are the same as the esp-idf wpa2 enterprise example.

### Build freeradius-alpine-raw docker container
```
docker build -t freeradius-alpine-raw .
```
### Running freshly-built docker container (in visible mode)

* **CLIENT_ADDRESS** is based on the AP Address
* **PRIVATE_KEY_PASSWORD** is based on provided key when certificates were generated.

* **CLIENT_SECRET** in this case would be **radiuspass**
```
sudo docker run -it -p 1812:1812/udp --restart=always -v pki:/etc/raddb/certs -e CLIENT_ADDRESS=192.168.1.1 -e CLIENT_SECRET=radiuspass -e PRIVATE_KEY_PASSWORD=password freeradius-alpine-raw:latest
```

Replace "-it" flag with "-d" to daemonize the process to background.
Replace CLIENT_ADDRESS with wifi-hotspot address, as long as CLIENT_SECRET and PRIVATE_KEY_PASSWORD with according values.
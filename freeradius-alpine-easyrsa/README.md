# FreeRadius over alpine:edge with EAP-TLS using Easy-RSA (freeradius-alpine-easyrsa)

## Steps to build container

### Build EasyRSA container (for certificate generation)
```
docker build easyrsa -t easyrsa
```

### Build freeradius-alpine-easyrsa docker container
```
docker build -t freeradius-alpine-easyrsa .
```
### Generate certificates (example client name: johndoe)
You can specify as many clients as you want (re-run this command each time, replacing word "johndoe" with any client names you like):
```
docker run -it -v pki:/easyrsa/pki easyrsa --digest=sha1 build-client-full johndoe
```
where *--digest=X* sets req/cert signing. X Options: md5, sha1, sha256, sha224, sha384, sha512

*NOTE:* CA and server certs signing can be modified in **easyrsa/entrypoint.sh** (default=sha1)

After generating certificates and private keys, you need to copy them manually from a "pki" docker volume to your client machine (total of 3 files):
* ca.crt (Central Authority Certificate)
* johndoe.crt (Your own certificate file)
* johndoe.key (Your own key file)
The files inside PKI docker volume can be located by navigating to /easyrsa/pki path.

E.g.:
```
CID=$(docker run -d -v pki:/easyrsa/ freeradius-alpine-easyrsa:latest)
```
```
docker cp $CID:/easyrsa ../certsdir
```

Inside certsdir you will find the certificates generated before.

### Running freshly-built docker container (in visible mode)

* **CLIENT_ADDRESS** is based on the AP Address
* **PRIVATE_KEY_PASSWORD** is based on provided key when certificates were generated.

* **CLIENT_SECRET** in this case would be **radiuspass**
```
sudo docker run -it -p 1812:1812/udp --restart=always -v pki:/etc/raddb/certs -e CLIENT_ADDRESS=192.168.1.1 -e CLIENT_SECRET=radiuspass -e PRIVATE_KEY_PASSWORD=password freeradius-alpine-easyrsa:latest
```

Replace "-it" flag with "-d" to daemonize the process to background.
Replace CLIENT_ADDRESS with wifi-hotspot address, as long as CLIENT_SECRET and PRIVATE_KEY_PASSWORD with according values.
# FreeRadius over alpine:edge with EAP-TLS (freeradius-alpine)

## Steps to build container

### Build EasyRSA container (for certificate generation)
```
docker build easyrsa -t easyrsa
```

### Build freeradius-alpine docker container
```
docker build -t freeradius-alpine .
```
### Generate certificates (example client name: johndoe)
You can specify as many clients as you want (re-run this command each time, replacing word "johndoe" with any client names you like).
```
docker run -it -v pki:/easyrsa/pki easyrsa build-client-full johndoe
```

After generating certificates and private keys, you need to copy them manually from a "pki" docker volume to your client machine (total of 3 files):
* ca.crt (Central Authority Certificate)
* johndoe.crt (Your own certificate file)
* johndoe.key (Your own key file)
The files inside PKI docker volume can be located by navigating to /easyrsa/pki path.

E.g.:
```
CID=$(docker run -d -v pki:/easyrsa/ freeradius-alpine:latest)
```
```
docker cp $CID:/easyrsa ./certsdir
```

Inside data you will find the certificates generated before.

### Running freshly-built docker container (in visible mode)
```
sudo docker run -it -p 1812:1812/udp --restart=always -v pki:/etc/raddb/certs -e CLIENT_ADDRESS=192.168.1.1 -e CLIENT_SECRET=password -e PRIVATE_KEY_PASSWORD=password freeradius-alpine:latest
```

Replace "-it" flag with "-d" to daemonize the process to background.
Replace CLIENT_ADDRESS with wifi-hotspot address, as long as CLIENT_SECRET and PRIVATE_KEY_PASSWORD with according values.

<!-- ### Client configuration
Now you can configure your AP with WPA2 Enterprise, AES, the server IP and client secret.
Use two certificates along with user private key to authenticate against freeradius tls.
Here's how you can do it for example:
![img](https://github.com/sxiii/fralp/raw/master/freeradius-auth.png)

### Windows and other P12 client configuration
If you need a P12-type certificate (for example, to import it on windows), you can use openssl to convert your results to it like this:
```
openssl pkcs12 -export -out certificate.pfx -inkey johndoe.key -in johndoe.crt -certfile ca.crt
```
Be sure to state all 3 files correctly and you'll get "certificate.pfx" as output that you'll be able to use on windows and other OSes. -->

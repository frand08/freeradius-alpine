# Create client certificates using Mbed TLS

**NOTE:** Right now, we need to copy the CA certificate and key from OpenSSL or similar to use this tool with CA signed certificate option.

In order to build Mbed TLS, just enter at the command line:

```
mkdir build && cd build
cmake ../mbedlts
make
```

Now, we can create the client certificates based on CA certificates. First, go back to the previous folder, and create the output folder where the client certificates will be stored, e.g., **out**

```
cd ..
mkdir out
```
Now, create the client key, e.g.,
```
./build/programs/pkey/own_gen_key \
type=rsa \
rsa_keysize=1024 \
secret_id=fadfad08 \
secret_md5=fadfad08fadfad08fadfad08fadfad08 \
filename=./out/wpa_client.key
```
where secret_id and secret_md5 must be in hex.

Then, create the client certificate, which can be self signed or CA signed.

If you want to create a CA signed certificate, store the CA cert and key files in **ca_certs** (or the folder you want), then edit the corresponding command.
- CA signed, e.g.:
```
./build/programs/x509/own_cert_write \
issuer_crt=ca_certs/wpa2_ca.pem \
issuer_key=ca_certs/wpa2_ca.key \
subject_key=./out/wpa_client.key \
subject_name=CN=fadfad08,O=utn,C=AR \
not_before=20010101000000 \
not_after=20311231235959 \
secret_id=fadfad08 \
selfsign=0 \
secret_md5=fadfad08fadfad08fadfad08fadfad08 \
output_file=./out/wpa2_client.crt
```

- Self signed, e.g.:
```
./build/programs/x509/own_cert_write \
issuer_key=./out/wpa_client.key \
issuer_name=CN=fadfad08,O=utn,C=AR \
not_before=20010101000000 \
not_after=20311231235959 \
selfsign=1 \
secret_id=fadfad08 \
secret_md5=fadfad08fadfad08fadfad08fadfad08 \
output_file=./out/wpa2_client.crt
```
where secret_id and secret_md5 must be in hex as before

Finally, copy all the required certificates in the certs folder, or you can save them in another folder, editing the Dockerfile properly.

# TODO
- Create all credentials using this tool
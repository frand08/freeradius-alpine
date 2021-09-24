# OpenSSL certificates creation

- Edit the ca.cnf with the info required (e.g., countryName, stateOrProvinceName, etc), then run
```
make ca.pem
```
followed by
```
make ca.der
```

- Edit server.cnf, same as before, then run
```
make server.pem
```
and give the output files read permission
```
chmod +r server.pem server.key
```
- Edit client.cnf, then run
```
make client.pem
```
and give the output files read permission
```
chmod +r client.pem client.key
```
- Create dh file
```
openssl dhparam -check -text -5 -out wpa2_dh.pem 2048
```

Finally, copy the created certificates in a folder to use them.

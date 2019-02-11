## Create Root CA

```shell
# Create Root CA Key
openssl genrsa -out local-ca.key 4096

# Create Root CA Certificate
openssl req -x509 -new -nodes -key local-ca.key -sha256 -days 1825 -out local-ca.crt
```

## Create self signed SSL certificate

```shell
# Create SSL Certificate CSR with alternate name
openssl req -newkey rsa:2048 -nodes -keyout local.key -subj "/C=US/ST=CA/O=Localhost, Inc./CN=*.local" -out local.csr

# Create SSL Certificate
openssl x509 -req -extfile <(printf "subjectAltName=DNS:local,DNS:*.local") -days 1825 -in local.csr -CA local-ca.crt -CAkey local-ca.key -CAcreateserial -out local.crt

# Create SSL Bundled Certificate
cat local.crt local-ca.crt > local.pem
```

## Add self signed SSL CA to system keychain

* For macOs `sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain local-ca.crt`
* For Linux `sudo cp local-ca.crt /usr/local/share/ca-certificates/local-ca.crt && sudo update-ca-certificates`
* For Chome under Linux try  https://chromium.googlesource.com/chromium/src/+/master/docs/linux_cert_management.md
* * `sudo apt-get install libnss3-tools`
* * `certutil -d sql:$HOME/.pki/nssdb -A -t "C,," -n local-ca -i local-ca.crt`

## Download current generated certificate (from github)

* `wget -O - -q --no-check-certificate https://raw.github.com/webinv/ssl-local/master/local.key > local.key` - local.key
* `wget -O - -q --no-check-certificate https://raw.github.com/webinv/ssl-local/master/local.pem > local.pem` - local.pem
* `wget -O - -q --no-check-certificate https://raw.github.com/webinv/ssl-local/master/local-ca.crt > local-ca.crt` - local-ca.crt
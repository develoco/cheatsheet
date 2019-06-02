# Become a Certificate Authority (CA)

Generate the CA's private key:

    openssl genrsa -des3 -out myCA.key 2048

Generate a root certificate:

    openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem

Manually add the certificate to all devices.

## macOS

Import `myCA.pem` to your login (or some other) keychain and grant it trust permissions.

    security add-trusted-cert -r trustRoot -k personal myCA.pem

Or add it system-wide with `-d` (need administrator password)

    security add-trusted-cert -d -r trustRoot -k personal myCA.pem

# Sign certificates with your CA

First, we create a private key:

    openssl genrsa -out dev.mergebot.com.key 2048

Then we create a CSR:

    openssl req -new -key dev.mergebot.com.key -out dev.mergebot.com.csr

Create a `dev.mergebot.com.ext` with the following SAN (Subject Alternative Name) configuration:

```
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = dev.mergebot.com
DNS.2 = dev.mergebot.com.192.168.1.19.xip.io
```

Create the certificate:

    openssl x509 -req -in dev.mergebot.com.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial -out dev.mergebot.com.crt -days 1825 -sha256 -extfile dev.mergebot.com.ext

Now configure web server with the private key and the certificate.

## Nginx

Open the site’s Nginx configuration file and add `ssl_certificate`, `ssl_certificate` entries:

    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    ssl_certificate /etc/letsencrypt/live/pluto.ashleyrich.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/pluto.ashleyrich.com/privkey.pem;

# Resources

- [How to Create Your Own SSL Certificate Authority for Local HTTPS Developmen]([https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/)

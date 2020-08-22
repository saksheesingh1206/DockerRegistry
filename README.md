For Sel signed Docker registery:

Use below command or docker compose

sudo docker run -d -p 5000:5000 --restart=always --name registry \
  -v /certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
  OR
##################docker-compose.yml################################
version: '3.0'

services:

  registry:
    container_name: docker-registry
    restart: always
    image: registry:2
    environment:
      REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt
      REGISTRY_HTTP_TLS_KEY: /certs/domain.key
    ports:
      - 5000:5000
    volumes:
      - docker-registry-data:/var/lib/registry
      - ./certs:/certs

volumes:
  docker-registry-data: {}
  #####################################################################
  
  root@master-node:/docker-registry# vi openssl.conf
[ req ]
distinguished_name = req_distinguished_name
x509_extensions     = req_ext
default_md         = sha256
prompt             = no
encrypt_key        = no

[ req_distinguished_name ]
countryName            = "GB"
localityName           = "London"
organizationName       = "Just Me and Opensource"
organizationalUnitName = "YouTube"
commonName             = "172.16.160.129"
emailAddress           = "test@example.com"

[ req_ext ]
subjectAltName = IP:172.16.160.129

[alt_names]
DNS = "172.16.160.129"

#############################################################################################################################

openssl req  -x509 -newkey rsa:4096 -days 365 -config /docker-registry/openssl.conf  -keyout /docker-registry/certs/domain.key -out /docker-registry/certs/domain.crt

#############################################################################################################################

On the worker node:



  

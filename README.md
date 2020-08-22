For Self signed Docker registery:
1. Create openssl.conf
2. Genreate Self signed Cert and key
3. run a docker container for docker-registry
4. pull an image from public docker registry
5. MAke some changes to the image , tag it  and upload to the private registry.
6. You will face an error "The push refers to a repository [192.168.0.2:5000/busybox]
Get https://192.168.0.2:5000/v1/_ping: x509: certificate signed by unknown authority"
follow below steps:
#############################################################################################################################
mkdir -p /etc/docker/certs.d/<ip>:5000
cd /certs
cp /certs/domain /etc/docker/certs.d/<ip>/
cd /etc/docker/certs.d/192.168.0.2:5000/
mv domains.crt ca.crt
#reload docker daemon to use the certificate
sudo service docker reload
############################################################################################################################
 7. Now pull on worker nodes - you will face same issue copy the ca.cert on the same location shared above. 
 
 
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

openssl req  -x509 -newkey rsa:4096 -days 365 -config /docker-registry/openssl.conf  -keyout /certs/domain.key -out /certs/domain.crt

#############################################################################################################################

On the worker node:
copy domain.crt{ca.crt} at /etc/docker/cert.d/ca.crt -- this will autrhenticate.



  

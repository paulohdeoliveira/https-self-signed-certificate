
#### Geração Certificado HTTPS
>[!IMPORTANT]
> O hostname precisa ser igual ao colocado no campo DNS.1 do certificado.
> O IP do servidor precisa ser informado no campo IP.1 do certificado.

#### Gerar certificado CA

```
openssl req -x509 -sha256 -days 1825 -nodes -newkey rsa:2024 -subj "/CN=mynetwork/C=BR/L=Sao Paulo" -keyout CA-private.key -out CA-public.crt
```
#### Gerar chave privada 

```
openssl genrsa -out pihole-private.key 2024
```
#### Gerar CSR
```
openssl req -new -key pihole-private.key -out pi-hole.csr -config csr.conf
```
#### Criar certificado assinado

```
openssl x509 -req -in pi-hole.csr -CA CA-public.crt -CAkey CA-private.key -CAcreateserial -out pihole-public.crt -days 730 -sha256 -extfile cert.conf
```

#### Certificado Full Chain
```
cat pihole-private.key pihole-public.crt | tee pihole-combined.pem
```
#### Copiar certificado para a pasta certs
```
sudo cp pihole-combined.pem /etc/pihole/
```

#### Configurar Certificado no NGINX ####
```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name domain_name;
    ssl_certificate /etc/nginx/certs/ssl_certificate.crt;
    ssl_certificate_key /etc/nginx/certs/ssl_certificate.key;
    ssl_protocols TLSv1.3;

    location / {
        proxy_pass http://localhost:9392;

        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Proto $scheme;
    }
}
```

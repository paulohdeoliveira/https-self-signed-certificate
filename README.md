
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

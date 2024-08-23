
#### Configuração do HTTPS no Pi-hole
>[!IMPORTANT]
> O hostname precisa ser igual ao colocado no DNS.1 do certificado


#### Gerar certificado CA

```
openssl req -x509 -sha384 -days 1825 -nodes -newkey rsa:4096 -subj "/CN=mynetwork/C=BR/L=Sao Paulo" -keyout CA-private.key -out CA-public.crt
```
#### Gerar chave privada 
```
openssl genrsa -out pihole-private.key 4096
```
#### Gerar CSR
```
openssl req -new -key pihole-private.key -out pi-hole.csr -config csr.conf
```
#### Criar certificado assinado

```
openssl x509 -req -in pi-hole.csr -CA CA-public.crt -CAkey CA-private.key -CAcreateserial -out pihole-public.crt -days 730 -sha384 -extfile cert.conf
```

#### Criar pasta para o certificado
```
sudo mkdir /etc/lighttpd/certs
```

#### Certificado Full Chain
```
cat pihole-private.key pihole-public.crt | tee pihole-combined.pem
```
#### Copiar certificados para a pasta certs
```
sudo cp pihole-combined.pem /etc/lighttpd/certs/
```

#### Copiar configuração lighttps

```
sudo cp 20-https.conf /etc/lighttpd/conf-available/
```
#### Criar link simbólico para o arquivo de configuração

```
sudo ln -s .../conf-available/20-https /etc/lighttpd/conf-enabled/20-https.conf
```

#### Instalar pacote
```
sudo apt install lighttpd_mod_openssl
```

#### Reiniciar lighttpd
```
sudo systemctl restart lighttpd

```

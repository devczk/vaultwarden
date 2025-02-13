# vaultwarden
Guia para gestão de senhas selfhosted sem custos adicionais, com uma plataforma que gere a nível de permissões, organizações e diversos features.

O VaultWarden é uma ferramenta de gestão de senhas que pode ser hospedado localmente (selfhosted), oferecendo autenticação de dois fatores, gerenciamento de usuários e acesso multinível. Portanto, é uma solução para gerenciamento de informações individual e em âmbito corporativo.

Segue abaixo guia para instalação do Vaultwarden no Linux utilizando Docker:
1) Crie um diretório para instalar o VaultWarden
-> mkdir vaultwarden
2) Para utilizar o VaultWarden é necessário configurar um Certificado SSL para garantir que a conexão seja criptografada de forma segura. Existem diversos tutoriais na internet de como utilizar o certbot e demais ferramenas para gerar os arquivos de certificado. Uma vez que tenha esses arquivos, armazena-los diretamente na pasta criada do VaultWarde, o diretório ficará com a seguinte estrutura:

-> docker-compose.yml
-> cert
   -> cert.crt
   -> cert.key
   -> fullchain.crt
   -> intermediate.crt
-> nginx.conf

3) Abaixo segue composição do arquivo docker-compose.yml:

version: "3.8"

services:
  vaultwarden:
    container_name: vaultwarden
    image: vaultwarden/server:latest
    environment:
      - WEBSOCKET_ENABLED=true
      - SIGNUPS_ALLOWED=true
      - ADMIN_TOKEN=<@@Kkw88dby6Vini369>  # Substitua por um token seguro para acessar
    volumes:
      - ./vw-data:/data
    networks:
      - vaultwarden-net
    restart: unless-stopped
    expose:
      - "80"  # O serviço do Vaultwarden estará disponível na rede interna na porta 80

  nginx:
    container_name: nginx
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf  # Arquivo de configuração do Nginx
      - ./cert:/etc/ssl/certs  # Certificados SSL (mapear o diretório onde os certificados estão)
    ports:
      - "80:80"  # Expondo a porta 80 do Nginx
      - "443:443"  # Expondo a porta 443 para SSL
    networks:
      - vaultwarden-net
    depends_on:
      - vaultwarden
    restart: unless-stopped

networks:
  vaultwarden-net:
    driver: bridge

4) No arquivo acima, definimos a importação do VaultWarden e do NGINX para criação do server web.

5) Após realizar os passos anteriores, pode construir o container através do comando:
-> docker-compose up -d

6) Para verificar se os containers estão rodando:
-> docker-compose ps

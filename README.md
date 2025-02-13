# Guia de instalação do VaultWarden no Linux
> Guia para gestão de senhas selfhosted sem custos adicionais, com uma plataforma que gere a nível de permissões, organizações e diversos features.

### O VaultWarden é uma ferramenta de gestão de senhas que pode ser hospedado localmente, oferecendo autenticação de dois fatores, gerenciamento de usuários e acesso multinível. Portanto, é uma solução para gerenciamento de informações individual e em âmbito corporativo.

Segue abaixo guia para instalação do Vaultwarden no Linux utilizando Docker:
### 1) Crie um diretório para instalar o VaultWarden
` mkdir vaultwarden`
### 2) Para utilizar o VaultWarden é necessário configurar um Certificado SSL para garantir que a conexão seja criptografada de forma segura. Existem diversos tutoriais na internet de como utilizar o certbot e demais ferramenas para gerar os arquivos de certificado. Uma vez que tenha esses arquivos, armazena-los diretamente na pasta criada do VaultWarde, o diretório ficará com a seguinte estrutura:

![Captura de tela 2025-02-13 163003](https://github.com/user-attachments/assets/7c9ea39e-a553-4d6d-9eff-1307e77ce2ee)

![Captura de tela 2025-02-13 163013](https://github.com/user-attachments/assets/fab100b1-9256-4a5c-8c1f-c4070db0ed6f)

### 3) Abaixo segue composição do arquivo docker-compose.yml:
```
version: "3.8"

services:
  vaultwarden:
    container_name: vaultwarden
    image: vaultwarden/server:latest
    environment:
      - WEBSOCKET_ENABLED=true
      - SIGNUPS_ALLOWED=true
      - ADMIN_TOKEN=<--palavra--passe-->  # Substitua por um token seguro para acessar
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
```
### 4) No arquivo acima, definimos a importação do VaultWarden e do NGINX para criação do server web.

### 5) Após realizar os passos anteriores, pode construir o container através do comando:

## Interface de Utilização do VaultWarden
 `docker-compose up -d`

### 6) Para verificar se os containers estão rodando:
`docker-compose ps`

## Visualização da Interface:

### Tela de Login:
![Captura de tela 2025-02-13 163321](https://github.com/user-attachments/assets/e1d88300-188a-491d-82c9-bfc45bf3a3b3)
### Tipos de Registros que Podem ser Armazenados
![Captura de tela 2025-02-13 163444](https://github.com/user-attachments/assets/10b8af41-ce8c-4c7d-b2f0-5e75f01a44c7)
### Opções de Autenticação:
![Captura de tela 2025-02-13 163509](https://github.com/user-attachments/assets/de90038b-66ee-451e-80a5-bf6ae0b45357)
### Gerador de Senhas Complexas
![Captura de tela 2025-02-13 163519](https://github.com/user-attachments/assets/2fa3c14e-103f-4da0-9c0b-11884d04b4c1)
### Relatórios de Vulnerabilidade e Análise de Registros Internos 
![Captura de tela 2025-02-13 163547](https://github.com/user-attachments/assets/33e4989e-727b-43db-b525-f9199acdf232)



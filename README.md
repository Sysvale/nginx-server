# nginx-server

## Configuração do container do NGINX

Inicialmente faça uma cópia do arquivo `.env.example` e insira um email de suporte que o certbot possa utilzar:
```
cp .env.example .env
```

Verifique a configuração correta com o comando:
```
docker-compose config
```

Crie a rede que será utilizada pelo container do nginx e pelos containers que serão gerenciados pelo mesmo, com o comando:
```
docker network create nginx
```

Para iniciar o container em background, use:
```
docker-compose up -d
```

## Configuração dos containers que serão conectados a rede

Para cada container é necessário que seja estabelecida uma conexão com a rede no nginx para que o proxy possa funcionar corretamente. Uma configuração simples seria adicionar a rede do nginx como rede padrão do container. Essa mudança pode ser feita, adicionando as seguintes linhas ao arquivo `docker-compose.yml`:
```yml
networks:
  default:
    external:
      name: nginx
```

Após a inserção dos containers na rede do nginx, é necessário criar arquivos de configuração que redirecionam o trafego da rede. Abaixo encontra-se o exemplo de um arquivo de configuração do nginx:
```conf
server {
    listen 443 ssl;

    server_name DOMAIN;

    ssl_certificate /etc/letsencrypt/live/DOMAIN/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/DOMAIN/privkey.pem;

    location / {
        proxy_pass http://CONTAINER_NAME:8000/;
    }
}
```

**Nota:** Para cada novo arquivo de configuração criado, o container do nginx deve ser parado com o `docker-compose down` e executado novamente com o comando ja citado. Isso é necessário para que o nginx carregue os novos arquivos de configuração corretamente.

**Nota:** O mesmo nome de domínio usado em `server_name` deve ser utilizado também em `ssl_certificate` e `ssl_certificate_key`.

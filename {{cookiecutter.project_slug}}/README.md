# {{cookiecutter.project_name}}

## Ambiente de desenvolvimento

```
git clone https://github.com/{{cookiecutter.github_user}}/{{cookiecutter.project_slug}}
cd {{cookiecutter.project_slug}}
docker-compose -f dev.yml build
docker-compose -f dev.yml up (-d)
```
Se for partir do zero, execute os seguintes comandos:
```
docker-compose -f dev.yml exec web python manage.py createsuperuser
```

Como alternativa, pode-se restaurar a partir da execução anterior ou de uma máquina diferente:
```
docker-compose -f dev.yml exec db list-backups
docker-compose -f dev.yml exec db restore <backup-timestamp>
```

Para o desenvolvimento local é utilizado um servidor interno do django (leia comando runserver) e a porta 8000 é exposta ao host. O diretório da web local é mapeado para o
container e as mudanças locais serão refletidas no container, facilitando muito o processo de desenvolvimento.

## Ambiente de Produção

O ambiente de produção é semelhante ao local, mas requer a criação do arquivo .env
baseado no exemplo .env.dev e uso do arquivo `prod.yml`
onde é usado o `dev.yml`.

Para a produção, um servidor gunicorn é usado para servir o Django
e a porta 8000 está exposta a outros serviços. O servidor Nginx
é usado como um proxy para o Django e para servir arquivos estáticos e de mídia.

### Importante

Não se esqueça de definir adequadamente o ambiente. Especialmente ALLOWED_HOSTS.

Existe um bug conhecido no Django CMS causando uma falha no carregamento / endereço.
Se você tiver 404 acessando o site (por exemplo, http://localhost) vá diretamente para /admin e crie a primeira página manualmente na seção DJANGO CMS -> Pages.

## Backups

Para preparar o backup, execute o seguinte comando:
```
docker-compose -f dev.yml exec db backup
```

Para listar todos os backups, execute o seguinte comando:
```
docker-compose -f dev.yml exec db list-backups
```

Para restaurar o backup escolhido, execute o seguinte comando:
```
docker-compose -f dev.yml exec db restore <backup-timestamp>
```

Os arquivos de backup estão localizados no diretório `/backups`. Utilize os comandos abaixo para copiar os arquivos do contêiner para a máquina local:

```
sudo docker cp container_id:/backups/<sql_backup> ./
sudo docker cp container_id:/backups/<media_backup> ./
```

Para copiar arquivos da máquina para o container, faça:
```
sudo docker cp <sql_backup> container_id:/backups/
sudo docker cp <media_backup> container_id:/backups/
```

## Logs

Os logs do nginx podem ser encontrados em `/var/log/nginx/error_{{cookiecutter.project_slug}}.log`
e `/var/log/nginx/access_{{cookiecutter.project_slug}}.log`.

Os logs do serviço da web estão em `/var/log/{{cookiecutter.project_slug}}/access.log`,
`/var/log/{{cookiecutter.project_slug}}/error.log` e `/var/log/{{cookiecutter.project_slug}}/django.log`.

Pode-se vê-los executando:
```
docker-compose -f dev.yml exec <service> tail [-F] <path to log>
```

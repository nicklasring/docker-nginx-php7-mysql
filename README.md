# docker-nginx-php7-mysql
Development Docker Containers to quickly start a new project

### Installation

- Clone this repository
- Create the folder 'mysql'
- Create the folder 'webroot'
- Set your mysql connection details as environment variables in docker-compose.yml

### Starting the app & db containers

```shell
docker-compose up -d
```

### Reloading the webserver

```shell
docker-compose exec app bash

# Inside the container
supervisorctl restart nginx
```

### TODO

- [ ] Add nginx configs as a volume
- [ ] Optimize supervisord config

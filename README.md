# Docker Project - Wordpress / mariadb / phpmyadmin

### Objective: 
#### - To create a Wordpress website (frontend) using mariadb (backend) and phpmyadmin (db management)

#### Create a .env file
```bash
MYSQL_DATABASE=wordpress
MYSQL_USER=wp_user
MYSQL_PASSWORD=wp_password
MYSQL_ROOT_PASSWORD=root_password
```

#### Create a docker-compose.yaml file
```yaml
version: "3.8"

services:

  database:
    # We use a mariadb image which supports both amd64 & arm64 architecture
    image: mariadb:10.6.4-focal
    restart: unless-stopped
    ports:
      - 3306:3306
    env_file: .env
    environment:
      MYSQL_ROOT_PASSWORD: '${MYSQL_ROOT_PASSWORD}'
      MYSQL_DATABASE: '${MYSQL_DATABASE}'
      MYSQL_USER: '${MYSQL_USER}'
      MYSQL_PASSWORD: '${MYSQL_PASSWORD}'
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - wordpress-network
    deploy:
      resources:
        limits:
          memory: 2048m

  phpmyadmin:
    depends_on:
      - database
    image: phpmyadmin/phpmyadmin
    restart: unless-stopped
    ports:
      - 8081:80
    env_file: .env
    environment:
      PMA_HOST: database
      MYSQL_ROOT_PASSWORD: '${MYSQL_ROOT_PASSWORD}'
    networks:
      - wordpress-network

  wordpress:
    depends_on:
      - database
    image: wordpress:6.2.2-apache
    restart: unless-stopped
    ports:
      - 8080:80
    env_file: .env
    environment:
      WORDPRESS_DB_HOST: database:3306 # use the same name as database service
      WORDPRESS_DB_NAME: '${MYSQL_DATABASE}'
      WORDPRESS_DB_USER: '${MYSQL_USER}'
      WORDPRESS_DB_PASSWORD: '${MYSQL_PASSWORD}'
    volumes:
      - ./wp-content:/var/www/html/wp-content
    networks:
      - wordpress-network

volumes:
  db-data:

networks:
  wordpress-network:
    driver: bridge  

```


#### run the docker-compose.yaml file
```sh
docker-compose up -d
```

<img width="1065" alt="Screenshot 2023-12-09 at 19 30 00" src="https://github.com/frank-goa/docker-project---wordpress_mariadb_phpmyadmin/assets/137857643/c5139381-692a-4500-97d2-f036973d118d">

<img width="1104" alt="Screenshot 2023-12-09 at 19 32 43" src="https://github.com/frank-goa/docker-project---wordpress_mariadb_phpmyadmin/assets/137857643/03b99ac4-9bd4-4462-a0b2-c45fba0c18e3">

<img width="1111" alt="Screenshot 2023-12-09 at 19 32 53" src="https://github.com/frank-goa/docker-project---wordpress_mariadb_phpmyadmin/assets/137857643/424408de-f5cb-4611-8bdd-2d13bf367a40">

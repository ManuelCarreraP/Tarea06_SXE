# Tarea06_SXE
Manuel Carrera Pazó
___________
#### 1º PASO
Lo primero que tenemos que hacer sería en la ruta que nosotros queramos crear el fichero docker-compose.yml  

```bash
touch docker-compose.yml
```

<img width="539" height="29" alt="Captura desde 2025-10-13 11-42-29" src="https://github.com/user-attachments/assets/f13f85ad-a48e-4a72-a0ef-54d335d2e760" />  
<br><br>
Una vez tenemos el archivo creado, lo abriremos con el siguiente comando 

```bash
nano docker-compose.yml
```
<img width="539" height="29" alt="image" src="https://github.com/user-attachments/assets/b178ab0f-f4d0-4267-b2ff-5ecd95ce68a6" />


Y le introduciremos el texto que está a continuación

```bash
services:
  db:
    image: mariadb:10.11
    container_name: prestashop_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-p${MYSQL_ROOT_PASSWORD}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    volumes:
      - db_data:/var/lib/mysql

  prestashop:
    image: prestashop/prestashop:latest
    container_name: prestashop_app
    depends_on:
      db:
        condition: service_healthy
    restart: always
    ports:
      - "8080:80"
    environment:
      DB_SERVER: ${DB_SERVER}
      DB_NAME: ${DB_NAME}
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
      PS_INSTALL_AUTO: 1
      PS_LANGUAGE: es
      PS_COUNTRY: es
      ADMIN_MAIL: ${ADMIN_MAIL}
      ADMIN_PASSWD: ${ADMIN_PASSWD}
    volumes:
      - prestashop_data:/var/www/html

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: prestashop_phpmyadmin
    depends_on:
      db:
        condition: service_healthy
    restart: always
    ports:
      - "8081:80"
    environment:
      PMA_HOST: ${DB_SERVER}
      PMA_USER: ${DB_USER}
      PMA_PASSWORD: ${DB_PASSWORD}

volumes:
  db_data:
  prestashop_data:
```  


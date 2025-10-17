# Tarea06_SXE
Manuel Carrera Pazó
___________
#### 1º PASO
Lo primero que tenemos que hacer sería en la ruta que nosotros queramos crear el fichero `docker-compose.yml`  

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


Y le introduciremos el texto que está a continuación:

```bash
services:
  db:
    image: mariadb:10.6
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    command: --default-authentication-plugin=mysql_native_password
    volumes:
      - db_data:/var/lib/mysql
    
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "${MYSQL_USER}", "-p${MYSQL_PASSWORD}"] 
      timeout: 15s
      retries: 10
      interval: 10s
      start_period: 30s
      
  prestashop:
    image: prestashop/prestashop:8.1
    depends_on:
      - db
    environment:
      DB_SERVER: db
      DB_NAME: ${MYSQL_DATABASE}
      DB_USER: ${MYSQL_USER}
      DB_PASSWD: ${MYSQL_PASSWORD}
      PS_INSTALL_AUTO: 1
      PS_DOMAIN: localhost:8080
      PS_LANGUAGE: es
      PS_COUNTRY: ES
      ADMIN_MAIL: ${ADMIN_MAIL}
      ADMIN_PASSWD: ${ADMIN_PASSWD}
    ports:
      - "8080:80"
    volumes:
      - ps_data:/var/www/html

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    depends_on:
      - db
    environment:
      PMA_HOST: db
      PMA_USER: ${MYSQL_USER}
      PMA_PASSWORD: ${MYSQL_PASSWORD}
    ports:
      - "8081:80"

volumes:
  db_data:
  ps_data:
```
<br><br>
Este fragmento del código sirve para verificar la salud de los contenedores y hacer que esperen a que el contenedor de la base de datos termine de ejecutarse correctamente antes de que los demás comiencen:
```bash
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "${MYSQL_USER}", "-p${MYSQL_PASSWORD}"] 
      timeout: 15s
      retries: 10
      interval: 10s
      start_period: 30s
```

___________

#### 2º PASO
Para no dejar contraseñas en el `docker-compose.yml`, vamos a crear un archivo `.env` en la misma carpeta que el `docker-compose.yml`.  
Con los siguientes comandos crearemos y abriremos el archivo `.env` 

```bash
touch .env
nano .env
```  
<img width="602" height="89" alt="image" src="https://github.com/user-attachments/assets/885fb4f9-8437-4f7b-bfdc-2977510320f1" />
<br><br>

A continuación añadiremos lo siguiente al fichero `.env`:  
```bash
# Variables
MYSQL_ROOT_PASSWORD=contraseña123
MYSQL_DATABASE=prestashop
MYSQL_USER=admin
MYSQL_PASSWORD=contraseña123
ADMIN_MAIL=manuelcarrera@gmail.com
ADMIN_PASSWD=AdminManuel
```

<img width="538" height="229" alt="image" src="https://github.com/user-attachments/assets/5b38cdde-43ee-4e91-8452-d9e80d0f7903" />


___________
#### 3º PASO
Una vez que ya tenemos el archivo `.env` con las variables creadas igual que en el archivo `docker-compose.yml`, con el healthcheck y todo lo demas incluido, pasaremos a las comprobaciónes ejecutando el siguiente comando:
```bash
docker compose up
```
<img width="1760" height="962" alt="image" src="https://github.com/user-attachments/assets/2b9f8c86-b5be-4128-aea5-cc124d4c4ad6" />

#### Comprobación:
Para realizar las comprobaciones abriremos desde nuestro navegador las siguientes IPs y veremos si se han creado correctamente:  

· `phpmyadmin` ⇾ http://localhost:8081/  

<img width="1920" height="1049" alt="image" src="https://github.com/user-attachments/assets/3cf02700-a74e-46a3-a607-658d4f635ad6" />
<br><br>  

· `prestashop` ⇾ http://localhost:8080/  

<img width="1920" height="1049" alt="image" src="https://github.com/user-attachments/assets/ce8949c8-b32f-4148-a3ad-a542d6e85333" />




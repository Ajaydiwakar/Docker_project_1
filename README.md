# Docker_project_1
# LinuxWorld_Docker_Project

Setting up the PHP development environment
We will make a directory web_dev : mkdir web_dev and will go inside: cd web_dev.
Then we will create docker-compose file with: nano docker-compose.yml
Inside we will place the following config:
Keep in mind that for the indentations we are using 2 spaces! 
version: '3.3'
services:
  web:
       image: php:7.3-apache
        container_name: php73
        volumes:
           - ./php:/var/www/html/
        ports:
          - 8008:80
          
          
          

MYSQL support
let's create a new file Dockerfile, inside the PHP directory. Place inside:
FROM php:7.3.3-apache
RUN apt-get update && apt-get upgrade -y RUN docker-php-ext-install mysqli
EXPOSE 80

This will base our MySQL on the PHP image, that we already have, update the container system, run  specific docker extensions supporting MySQL from within PHP and expose 80-port



Next, we will customize the docker-compose.yml :
We will replace the line: image: PHP
with
buid:
  context: ./php
  dockerfile: Dockerfile 

This will read the docker file which we have set previously and create web service out of it.

Now we will be building the MySQL service:
db:
  container_name: mysql8
  image: mysql:latest
  command: --default-authentication-plugin=mysql_native_password
  restart: always
  environment:
    MYSQL_ROOT_PASSWORD: root
    MYSQL_DATABASE: test_db
    MYSQL_USER: devuser
    MYSQL_PASSWORD: devpass
   ports:
       - 6033:3306

Notes: We are using default authentication for MySQL in order to be able to login to the MySQL database, then docker is hardcoding mysql_user, mysql_password and mysql_database with: deuser, devpass, and test_db, and is exposing externally port 6033 for the MySQL service.

One last change: we would like first start the MySQL then the DB service, so we will add to the web service config:
depends_on:
  - db

To test the PHP-MySQL connection inside of our index.php file we can specify:

<?php
$host = 'db';  //the name of the mysql service inside the docker file.
$user = 'devuser';
$password = 'devpass';
$db = 'test_db';
$conn = new mysqli($host,$user,$password,$db)
 if($conn->connect_error){
  echo 'connection failed'. $conn->connect_error;
}
echo 'successfully connected to MYSQL';
?>

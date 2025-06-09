# Laboratorium 12 - Tworzenie przykładowego pliku docker-compose.yaml. Uruchamianie i weryfikacja działania aplikacji wielokontenerowej. Budowanie obrazów w Docker Compose - wprowadzenie.

Polecenie:


Należy zbudować prosty plik docker-compose.yml, który pozwoli na uruchomienie znanego z innych zajęć, stack-a LEMP wraz z phpMyAdmin. Stack LEMP składa się z następujących usług składowych: 
---- L – dla Linux;
---- E – dla Nginx; 
---- M – dla MySQL; 
---- P – dla PHP.

Aplikacja powinna zawierać cztery kontenery (mikrousługi):

 -  jeden kontener dla Nginx,
 -  jeden kontener dla PHP (PHP-FPM),
 -  jeden kontener dla MySQL,
 -  jeden kontener dla phpMyAdmin.

Założenia dla poszczególnych mikrousług:
 

 - serwery wykorzystują obrazy ze zdefiniowanym tag-iem, udostępniane na
   DockerHub


 - serwery PHP, MySQL są przyłączone do sieci backend a Nginx do backend
   oraz frontend. Nginx ma wystawiony na świat zewnętrzy port 4001,

 - serwer Nginx ma wyświetlać stronę startową php (index.php),


 - serwer phpMyAdmin ma być dostępny na porcie 6001 i powinno być
   możliwe zalogowanie się do niego i założenie testowej bazy. Wobec
   tego proszę przemyśleć do której sieci powinien być dołączony i
   uzasadnić dlaczego.

W sprawozdaniu proszę umieścić plik docker-compose.yaml. W piku README.md należy podać wszystkie użyte polecenia wraz z wynikiem ich działania. 
Dodatkowo, należy dowieść, że: 

 - stack LEMP działa poprawnie i wyświetla domyślnie wymaganą stronę,

 - można zainicjować testową bazę danych

## Inicjalizacja i sprzątanie

    docker compose --env-file .env up

    docker compose down -v

## Finalny plik docker-compose

	
    version: '3.8'    //wersja składni compose
    
    services:     //początek opisu usług
      nginx:
        image: nginx:1.25
        container_name: nginx
        ports:
          - "4001:80"    //przekierowany port dla nginx
        volumes:
          - ./app:/var/www/html
          - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
        depends_on:
          - php
        networks:    //sieci frontend i backend dla nginx
          - frontend
          - backend
    
      php:   //kontener php
        image: php:8.2-fpm
        container_name: php
        volumes:
          - ./app:/var/www/html
        networks:    //sieci do php backend
          - backend
    
      mysql:   //kontener bazy danych mysql
        image: mysql:8.0
        container_name: mysql
        restart: always
        environment:
          MYSQL_DATABASE: ${MYSQL_DATABASE}
          MYSQL_USER: ${MYSQL_USER}
          MYSQL_PASSWORD_FILE: /run/secrets/mysql_user_password
          MYSQL_ROOT_PASSWORD_FILE: /run/secrets/mysql_root_password
        secrets:
          - mysql_root_password
          - mysql_user_password
        volumes:
          - db_data:/var/lib/mysql
        networks:
          - backend
    
      phpmyadmin:   //kontener phpmyadmin
        image: phpmyadmin/phpmyadmin:5.2
        container_name: phpmyadmin
        ports:
          - "6001:80"
        environment:
          PMA_HOST: mysql
          PMA_USER: ${MYSQL_USER}
          PMA_PASSWORD_FILE: /run/secrets/mysql_user_password
        secrets:
          - mysql_user_password
        depends_on:
          - mysql
        networks:  //sieci do phpmyadmin, frontend dla uzytkownikow, backend do sieci wewn.
          - frontend
          - backend
    
    volumes:
      db_data:
    
    networks:
      backend:
      frontend:
    
    secrets:  //pliki secretow
      mysql_root_password:
        file: ./secrets/mysql_root_password.txt
      mysql_user_password:
        file: ./secrets/mysql_user_password.txt




## Autor rozwiązania

Michał Grzegorz Małysz 
Grupa 6.8

## Autor zadania

Dr inż. Sławomir Przyłucki 

s.przylucki@pollub.pl

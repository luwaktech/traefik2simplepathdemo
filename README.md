# traefik2simplepathdemo

Demo Traefik 2 sebagai Reverse Proxy di Docker Swarm menggunakan cloud dengan contoh IP publik 54.169.31.229, yang dapat dipakai untuk mengakses situs dari internet

Berikut contoh dengan menggunakan Swarm untuk menjalankan Traefik 2 sebagai service reverse proxy dan 3 service lainnya, yaitu:
1.Hello World service
Docker Image : tutum/hello-world
Rule Path    : /
Port         : 80
Akses URL    : http://54.169.31.229/

2.Whoami service
Docker Image : jwilder/whoami
Rule Path    : /whoami
Port         : 80
Akses URL	   : http://54.169.31.229/whoami

3.Whoami service
Docker Image : containous/whoami
Rule Path    : /whoami2
Port         : 80
Akses URL	   : http://54.169.31.229/whoami2

4.Traefik 2
Api Raw Data : http://54.169.31.229:8080/api/rawdata
Dashboard	   : http://54.169.31.229:8080/dashboard


Cara menjalankan demo:
1. Buat network overlay baru di Swarm dengan nama traefik-public
$ docker network create --driver=overlay traefik-public

2. Deploy stack service traefik 2 memakai yml
$ docker stack deploy -c traefik2.yml demo

3. Deploy stack service aplikasi memakai yml juga
$ docker stack deploy -c app.yml demo

4. Cek docker service
$ docker service ls

4. Buka situs di web browser menggunakan IP Public/Domain
http://54.169.31.229/
http://54.169.31.229/whoami
http://54.169.31.229/whoami2

5. Bersihkan demo dengan cara remove semua service yang ada
$ docker service rm $(docker service ls -q)

6. Bersikah network 
$ docker network rm traefik-public 

































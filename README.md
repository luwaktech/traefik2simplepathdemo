# Traefik 2 Reverse Proxy dengan Rule Path di Docker Swarm

Demo Traefik 2 sebagai Reverse Proxy di Docker Swarm menggunakan cloud dengan contoh IP publik `54.169.31.229`, yang dapat dipakai untuk mengakses situs dari internet. Bisa juga mengganti IP publik dengan menggunakan Domain (misal `domainku.com`) sebagai Host rule

Berikut contoh dengan menggunakan Swarm untuk menjalankan Traefik 2 sebagai service reverse proxy dan 3 service lainnya, yaitu:
```
1.Hello World service
Docker Image : tutum/hello-world
Rule Path    : /
Akses Port   : 80
Akses URL    : http://54.169.31.229/ (demo URL)

2.Whoami service
Docker Image : jwilder/whoami
Rule Path    : /whoami
Akses Port   : 80
Akses URL    : http://54.169.31.229/whoami (demo URL)

3.Whoami service
Docker Image : containous/whoami
Rule Path    : /whoami2
Akses Port   : 80
Akses URL    : http://54.169.31.229/whoami2 (demo URL)

4.Traefik 2
Api Raw Data : http://54.169.31.229:8080/api/rawdata
Dashboard    : http://54.169.31.229:8080/dashboard
```
Berikut arsitektur reverse proxy
![Arsitektuk Traefik 2 demo](https://user-images.githubusercontent.com/12096917/66353172-9d485500-e98b-11e9-9f89-5233d857e888.JPG)

**Prasyarat**:
1. Demo ini dijalankan di Ubuntu
2. Docker sudah terinstall
3. Docker swarm sudah terbentuk\
Jika belum, maka bisa buat swarm dengan command dibawah ini:
```
$ docker swarm init
```

**Cara menjalankan demo**:
1. Buat network overlay baru di Swarm dengan nama traefik-public
```
$ docker network create --driver=overlay traefik-public
```

2. Unduh file konfigurasi yang dibutuhkan
```
$ curl https://raw.githubusercontent.com/luwaktech/traefik2simplepathdemo/master/traefik2.yml > traefik2.yml
$ curl https://raw.githubusercontent.com/luwaktech/traefik2simplepathdemo/master/app.yml > app.yml
```

3. Ubah host `54.169.31.229` pada file app.yml\
Ubah menggunakan IP publik server atau bisa juga diubah menggunakan hostname, misal `domainku.com`
```
$ vi app.yml
```

Contoh:
- traefik.http.routers.helloworld.rule=Host(**`54.169.31.229`**)
- traefik.http.routers.whoami.rule=Host(**`54.169.31.229`**) && Path(`/whoami`)
- traefik.http.routers.whoami2.rule=Host(**`54.169.31.229`**) && Path(`/whoami2`)


4. Deploy stack service traefik 2 memakai yml
```
$ docker stack deploy -c traefik2.yml demo
```

5. Deploy stack service aplikasi memakai yml juga
```
$ docker stack deploy -c app.yml demo
```

6. Cek docker service
```
$ docker service ls
```

7. Buka situs di web browser menggunakan IP Public/Domain
```
- http://54.169.31.229/
- http://54.169.31.229/whoami
- http://54.169.31.229/whoami2
```
- Whoami2
![Whoami2](https://user-images.githubusercontent.com/12096917/66353931-7b4fd200-e98d-11e9-8953-1edfd87b5002.png)

8. Buka Traefik API raw data URL 
`http://54.169.31.229:8080/api/rawdata`
- Raw data
![Raw data](https://user-images.githubusercontent.com/12096917/66353814-290eb100-e98d-11e9-8b47-c8cc2354182f.png)

- Router
![Router](https://user-images.githubusercontent.com/12096917/66353656-bf8ea280-e98c-11e9-9b0d-7e1a4f6f07ae.png)

- Service
![Service](https://user-images.githubusercontent.com/12096917/66353756-04b2d480-e98d-11e9-820e-9b524c40af5a.png)

9. Scale service whoami2 menjadi 3 instance
```
$ docker service scale demo_whoami2=3
```
Coba akses `http://54.169.31.229/whoami2` beberapa kali, terlihat bahwa IP nya akan berubah karena adanya load balancing dari Traefik 2

10. Bersihkan demo dengan cara remove semua service yang ada
```
$ docker service rm $(docker service ls -q)
```

11. Bersihkan network 
```
$ docker network rm traefik-public 
```

12. Leave swarm
```
$ docker swarm leave --force
```

**Penjelasan**
> Traefik2.yml
- Jika ingin menjalan traefik dibelakang nginx/haproxy, maka perlu mengubah port 80:80 menjadi `proxy port` pada docker, seperti dibawah ini:
```
    ports:
      # The HTTP port
      #- "80:80"
      # The HTTP port as proxy (jika dipakai dibelakang nginx/haproxy)
      - target: 80
        published: 80
        mode: host
      # The Web UI (enabled by --api.insecure=true)
      - "8080:8080"
```
Dengan mode:host, hal ini akan membuat docker instance untuk listen di host port

Youtube demo (versi video)  \
https://bit.ly/30TAD6o  


Referensi: \
Traefik 2 - https://docs.traefik.io/v2.0/  
Docker swarm - https://docs.docker.com/engine/swarm/ 


Semoga bisa membantu.\
Terima kasih.\
sibeeni





























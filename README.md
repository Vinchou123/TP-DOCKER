# TP-DOCKER

## TP1 : Premiers pas Docker

### I. Init

#### 1. Installation de Docker

#### 2. Vérifier que Docker est bien là

```
[vince@VM-DOCKER ~]$ systemctl status docker
○ docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; preset: disabled)
     Active: inactive (dead)
TriggeredBy: ○ docker.socket
       Docs: https://docs.docker.com
[vince@VM-DOCKER ~]$
```

```
[vince@VM-DOCKER ~]$ sudo systemctl start docker
[sudo] password for vince:
[vince@VM-DOCKER ~]$ sudo systemctl enable docker
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /usr/lib/systemd/system/docker.service.
[vince@VM-DOCKER ~]$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: disabled)
     Active: active (running) since Wed 2024-12-11 10:59:19 CET; 1min 5s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 49611 (dockerd)
      Tasks: 7
     Memory: 34.6M
        CPU: 546ms
     CGroup: /system.slice/docker.service
```

```
[vince@VM-DOCKER ~]$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

#### 3. sudo c pa bo

```
[vince@VM-DOCKER ~]$ sudo usermod -aG docker vince
```


#### 4. Un premier conteneur en vif

🌞 Lancer un conteneur NGINX

```
[vince@VM-DOCKER ~]$ docker run -d -p 9999:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
bc0965b23a04: Pull complete
650ee30bbe5e: Pull complete
8cc1569e58f5: Pull complete
362f35df001b: Pull complete
13e320bf29cd: Pull complete
7b50399908e1: Pull complete
57b64962dd94: Pull complete
Digest: sha256:fb197595ebe76b9c0c14ab68159fd3c08bd067ec62300583543f0ebda353b5be
Status: Downloaded newer image for nginx:latest
3e64ecfabdb39d16ea5c3852ee1a085aa717b2e87fe8f39f796c4255d389930b
```

🌞 Visitons

```
[vince@VM-DOCKER ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS                                     NAMES
3e64ecfabdb3   nginx     "/docker-entrypoint.…"   About a minute ago   Up 59 seconds   0.0.0.0:9999->80/tcp, [::]:9999->80/tcp   stupefied_merkle
```

```
[vince@VM-DOCKER ~]$ docker logs 3e64ecfabdb3
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/12/11 10:14:06 [notice] 1#1: using the "epoll" event method
2024/12/11 10:14:06 [notice] 1#1: nginx/1.27.3
2024/12/11 10:14:06 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2024/12/11 10:14:06 [notice] 1#1: OS: Linux 5.14.0-284.11.1.el9_2.x86_64
2024/12/11 10:14:06 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1073741816:1073741816
2024/12/11 10:14:06 [notice] 1#1: start worker processes
2024/12/11 10:14:06 [notice] 1#1: start worker process 28
```

```
[vince@VM-DOCKER ~]$ docker inspect  3e64ecfabdb3

```

```
[vince@VM-DOCKER ~]$ sudo ss -lnpt
[sudo] password for vince:
State      Recv-Q     Send-Q           Local Address:Port           Peer Address:Port     Process
LISTEN     0          128                    0.0.0.0:22                  0.0.0.0:*         users:(("sshd",pid=18996,fd=3))
LISTEN     0          4096                   0.0.0.0:9999                0.0.0.0:*         users:(("docker-proxy",pid=50199,fd=4))
LISTEN     0          128                       [::]:22                     [::]:*         users:(("sshd",pid=18996,fd=4))
LISTEN     0          4096                      [::]:9999                   [::]:*         users:(("docker-proxy",pid=50204,fd=4))

```

```
[vince@VM-DOCKER ~]$ sudo firewall-cmd --zone=public --add-port=9999/tcp --permanent
success
```

Sur le web : 

```
http://http://10.2.2.5:9999
```

```
Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```


🌞 On va ajouter un site Web au conteneur NGINX


```
[vince@VM-DOCKER ~]$ mkdir  /home/vince/nginx
```

```
[vince@VM-DOCKER ~]$ nano /home/vince/nginx/index.html
```

```
[vince@VM-DOCKER ~]$ nano /home/vince/nginx/site_nul.conf
```

```
[vince@VM-DOCKER ~]$ docker run -d -p 9999:8080 -v /home/vince/nginx/index.html:/var/www/html/index.html -v /home/vince/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf nginx
99de04ae7ccb3a1fee977648042a14b978a3ddf24ec604cc4c48d574a6e1777a
```

🌞 Visitons

```
[vince@VM-DOCKER ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                 NAMES
99de04ae7ccb   nginx     "/docker-entrypoint.…"   45 seconds ago   Up 43 seconds   80/tcp, 0.0.0.0:9999->8080/tcp, [::]:9999->8080/tcp   jovial_goodall
```

```
[vince@VM-DOCKER ~]$ sudo ss -lnpt | grep :9999
LISTEN 0      4096         0.0.0.0:9999      0.0.0.0:*    users:(("docker-proxy",pid=50521,fd=4))
LISTEN 0      4096            [::]:9999         [::]:*    users:(("docker-proxy",pid=50526,fd=4))
```

sur le web : 

http://10.2.2.5

```
MEOOOW
```


#### 5. Un deuxième conteneur en vif

🌞 Lance un conteneur Python, avec un shell

```
[vince@VM-DOCKER ~]$ docker run -it python bash
Unable to find image 'python:latest' locally
latest: Pulling from library/python
fdf894e782a2: Pull complete
5bd71677db44: Pull complete
551df7f94f9c: Pull complete
ce82e98d553d: Pull complete
5f0e19c475d6: Pull complete
abab87fa45d0: Pull complete
2ac2596c631f: Pull complete
Digest: sha256:220d07595f288567bbf07883576f6591dad77d824dce74f0c73850e129fa1f46
Status: Downloaded newer image for python:latest
```


```
root@49dc1ac6ec44:/# pip install aiohttp aioconsole
```

```
root@49dc1ac6ec44:/# python
Python 3.13.1 (main, Dec  4 2024, 20:40:27) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import aiohttp
>>>
```

### II. Images

#### 1. Images publiques

🌞 Récupérez des images

```
[vince@VM-DOCKER ~]$ docker pull python:3.11
3.11: Pulling from library/python
fdf894e782a2: Already exists
5bd71677db44: Already exists
551df7f94f9c: Already exists
ce82e98d553d: Already exists
2371bf9a39a3: Pull complete
0b3239f18dfa: Pull complete
de07a735a679: Pull complete
Digest: sha256:2c80c66d876952e04fa74113864903198b7cfb36b839acb7a8fef82e94ed067c
Status: Downloaded newer image for python:3.11
docker.io/library/python:3.11
```

```
[vince@VM-DOCKER ~]$ docker pull mysql:5.7
5.7: Pulling from library/mysql
20e4dcae4c69: Pull complete
1c56c3d4ce74: Pull complete
e9f03a1c24ce: Pull complete
68c3898c2015: Pull complete
6b95a940e7b6: Pull complete
90986bb8de6e: Pull complete
ae71319cb779: Pull complete
ffc89e9dfd88: Pull complete
43d05e938198: Pull complete
064b2d298fba: Pull complete
df9a4d85569b: Pull complete
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

```
[vince@VM-DOCKER ~]$ docker pull linuxserver/wikijs
Using default tag: latest
latest: Pulling from linuxserver/wikijs
72387e7898ce: Pull complete
ec7680b185bf: Pull complete
b80232684b80: Pull complete
52e5d6eefe42: Pull complete
f224b10b4dc1: Pull complete
d179dce5799c: Pull complete
ff211b9bebf2: Pull complete
979004086415: Pull complete
Digest: sha256:45ecf23a3bf849a0bf0c9e2d832aede159e98efd29fef70bbc7ff4dd87522eba
Status: Downloaded newer image for linuxserver/wikijs:latest
docker.io/linuxserver/wikijs:latest
```

```
[vince@VM-DOCKER ~]$ docker images
REPOSITORY           TAG       IMAGE ID       CREATED         SIZE
linuxserver/wikijs   latest    863e49d2e56c   4 days ago      465MB
python               latest    3ca4060004b1   7 days ago      1.02GB
python               3.11      342f2c43d207   7 days ago      1.01GB
nginx                latest    66f8bdd3810c   2 weeks ago     192MB
mysql                5.7       5107333e08a8   12 months ago   501MB
```

🌞 Lancez un conteneur à partir de l'image Python


```
[vince@VM-DOCKER ~]$ docker run -it python:3.11 bash
root@35c88d90d4fc:/# python --version
Python 3.11.11
```

#### 2. Construire une image

```
[vince@VM-DOCKER python_app_build]$ mkdir -p /home/vince/python_app_build
```
```
[vince@VM-DOCKER python_app_build]$ cd /home/vince/python_app_build
```
```
[vince@VM-DOCKER python_app_build]$ nano app.py
````
```
[vince@VM-DOCKER python_app_build]$ nano Dockerfile
```

🌞 Build l'image

```
[vince@VM-DOCKER python_app_build]$ cd /home/vince/python_app_build
```

```
[vince@VM-DOCKER python_app_build]$ docker build . -t python_app:version_de_ouf
[+] Building 340.0s (9/9) FINISHED                                                                                                           docker:default
 => [internal] load build definition from Dockerfile                                                                                                   0.4s
 => => transferring dockerfile: 274B                                                                                                                   0.1s
 => [internal] load metadata for docker.io/library/debian:bullseye-slim                                                                                2.3s
 => [internal] load .dockerignore                                                                                                                      0.4s
 => => transferring context: 2B                                                                                                                        0.1s
 => [1/4] FROM docker.io/library/debian:bullseye-slim@sha256:8118d0da5204dcc2f648d416b4c25f97255a823797aeb17495a01f2eb9c1b487                         23.3s
 => => resolve docker.io/library/debian:bullseye-slim@sha256:8118d0da5204dcc2f648d416b4c25f97255a823797aeb17495a01f2eb9c1b487                          0.6s
 => => sha256:69fb10dc82f9580a647bd4638e741b2338cb8e2575d2be6f0bacfcada936a617 30.25MB / 30.25MB                                                       7.9s
 => => sha256:8118d0da5204dcc2f648d416b4c25f97255a823797aeb17495a01f2eb9c1b487 4.54kB / 4.54kB                                                         0.0s
 => => sha256:43112fc2ced1dc71d4c92a1e42c3f831a2d5840ffa4d9fcd37802cd0594c1edf 1.02kB / 1.02kB                                                         0.0s
 => => sha256:4546fbfb0f90cb90fdbec49099cd4801600f8c92d3908b37529a930316016ed3 453B / 453B                                                             0.0s
 => => extracting sha256:69fb10dc82f9580a647bd4638e741b2338cb8e2575d2be6f0bacfcada936a617                                                             13.9s
 => [internal] load build context                                                                                                                      0.5s
 => => transferring context: 188B                                                                                                                      0.1s
 => [2/4] RUN apt-get update && apt-get install -y python3 python3-pip                                                                               232.9s
 => [3/4] RUN pip3 install emoji                                                                                                                      14.3s
 => [4/4] COPY app.py /app/app.py                                                                                                                      1.0s
 => exporting to image                                                                                                                                60.0s
 => => exporting layers                                                                                                                               59.5s
 => => writing image sha256:f93328df2dfc5b0be78e0245750a6f2fe6eadf1465d099927f0ceccfb4bc73b8                                                           0.1s
 => => naming to docker.io/library/python_app:version_de_ouf                                                                                           0.1s
```
```
[vince@VM-DOCKER python_app_build]$ docker images
REPOSITORY           TAG              IMAGE ID       CREATED         SIZE
python_app           version_de_ouf   f93328df2dfc   3 minutes ago   442MB
linuxserver/wikijs   latest           863e49d2e56c   4 days ago      465MB
python               latest           3ca4060004b1   7 days ago      1.02GB
python               3.11             342f2c43d207   7 days ago      1.01GB
nginx                latest           66f8bdd3810c   2 weeks ago     192MB
mysql                5.7              5107333e08a8   12 months ago   501MB
```


🌞 Lancer l'image

```
[vince@VM-DOCKER python_app_build]$ docker run python_app:version_de_ouf
Cet exemple d'application est vraiment naze 👎
```




## III. Docker compose

🌞 Créez un fichier docker-compose.yml

```
[vince@VM-DOCKER compose_test]$ ls
docker-compose.yml
```

🌞 Lancez les deux conteneurs avec docker compose

```
[vince@VM-DOCKER compose_test]$ docker compose up -d
WARN[0000] /home/vince/compose_test/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 3/3
 ✔ conteneur_nul Pulled                                                                                                                                4.7s
 ✔ conteneur_flopesque Pulled                                                                                                                          4.4s
   ✔ fdf894e782a2 Already exists                                                                                                                       0.0s
[+] Running 3/3
 ✔ Network compose_test_default                  Created                                                                                               4.5s
 ✔ Container compose_test-conteneur_nul-1        Started                                                                                               7.0s
 ✔ Container compose_test-conteneur_flopesque-1  Started                                                                                               6.9s
 ```

 🌞 Vérifier que les deux conteneurs tournent

 ```
 [vince@VM-DOCKER compose_test]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                                                 NAMES
e349c7b929fb   debian    "sleep 9999"             About a minute ago   Up About a minute                                                         compose_test-conteneur_flopesque-1
db900b965185   debian    "sleep 9999"             About a minute ago   Up About a minute                                                         compose_test-conteneur_nul-1
99de04ae7ccb   nginx     "/docker-entrypoint.…"   54 minutes ago       Up 54 minutes       80/tcp, 0.0.0.0:9999->8080/tcp, [::]:9999->8080/tcp   jovial_goodall
```

```
[vince@VM-DOCKER compose_test]$ docker compose ps
WARN[0000] /home/vince/compose_test/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
NAME                                 IMAGE     COMMAND        SERVICE               CREATED         STATUS         PORTS
compose_test-conteneur_flopesque-1   debian    "sleep 9999"   conteneur_flopesque   2 minutes ago   Up 2 minutes
compose_test-conteneur_nul-1         debian    "sleep 9999"   conteneur_nul         2 minutes ago   Up 2 minutes
```

```
[vince@VM-DOCKER compose_test]$ docker compose top
WARN[0000] /home/vince/compose_test/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
compose_test-conteneur_flopesque-1
UID    PID     PPID    C    STIME   TTY   TIME       CMD
root   55644   55600   0    12:25   ?     00:00:00   sleep 9999

compose_test-conteneur_nul-1
UID    PID     PPID    C    STIME   TTY   TIME       CMD
root   55636   55593   0    12:25   ?     00:00:00   sleep 9999
```

🌞 Pop un shell dans le conteneur conteneur_nul

```
[vince@VM-DOCKER compose_test]$ docker exec -it compose_test-conteneur_nul-1 /bin/sh
```
```
# apt update
# apt install iputils-ping
```

```
# ping conteneur_flopesque
PING conteneur_flopesque (172.18.0.3) 56(84) bytes of data.
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.3): icmp_seq=1 ttl=64 time=0.587 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.3): icmp_seq=2 ttl=64 time=0.154 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.3): icmp_seq=3 ttl=64 time=0.131 ms
^C
--- conteneur_flopesque ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 0.131/0.290/0.587/0.209 ms
#
```
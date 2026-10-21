[root@LXMASTER02 awx-17.1.0]# docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED       STATUS        PORTS                                               NAMES
174f0376ff43   ansible/awx:17.1.0   "/usr/bin/tini -- /u…"   5 years ago   Up 4 months   8052/tcp                                            awx_task
c30ac81a1fbc   ansible/awx:17.1.0   "/usr/bin/tini -- /b…"   5 years ago   Up 4 months   8052/tcp, 0.0.0.0:443->8053/tcp, :::443->8053/tcp   awx_web
e48e3e21b6bd   postgres:12          "docker-entrypoint.s…"   5 years ago   Up 4 months   5432/tcp                                            awx_postgres
ca75eb0a24dd   redis                "docker-entrypoint.s…"   5 years ago   Up 4 months   6379/tcp                                            awx_redis

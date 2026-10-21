[root@LXMASTER02 awx-17.1.0]# docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED       STATUS        PORTS                                               NAMES
174f0376ff43   ansible/awx:17.1.0   "/usr/bin/tini -- /u…"   5 years ago   Up 4 months   8052/tcp                                            awx_task
c30ac81a1fbc   ansible/awx:17.1.0   "/usr/bin/tini -- /b…"   5 years ago   Up 4 months   8052/tcp, 0.0.0.0:443->8053/tcp, :::443->8053/tcp   awx_web
e48e3e21b6bd   postgres:12          "docker-entrypoint.s…"   5 years ago   Up 4 months   5432/tcp                                            awx_postgres
ca75eb0a24dd   redis                "docker-entrypoint.s…"   5 years ago   Up 4 months   6379/tcp                                            awx_redis
docker exec -it awx_task awx-manage export_fixtures > /root/awx_data_export.json
docker cp /root/awx_data_export.json tools_ascender_1:/tmp/awx_data_export.json
docker exec -it tools_ascender_1 ascender-manage import_fixtures /tmp/awx_data_export.json
docker exec -it awx_task awx-manage export_fixtures > awx_data_export.json
[root@LXMASTER02 u01]# ll -ltr
total 4
drwxr-xr-x 3 root root  17 Apr  8  2020 db
drwxr-xr-x 3 root root  17 Mar 25  2021 certifications
drwxr-xr-x 4 root root  31 Mar 25  2021 install
drwxr-xr-x 3 root root  17 Mar 26  2021 python
-rw-r----- 1 root root 100 Oct 21 13:17 awx_data_export.json
[root@LXMASTER02 u01]# cat awx_data_export.json
Unknown command: 'export_fixtures'. Did you mean export_emails?
Type 'awx-manage help' for usage.
[root@LXMASTER02 u01]#
docker exec -it awx_task awx-manage dumpdata \
  --indent=2 \
  --exclude=auth.Permission \
  --exclude=contenttypes \
  --exclude=main.activitystream \
  --exclude=sessions \
  > /u01/awx_data_export.json

docker exec -it tools_ascender_1 ascender-manage loaddata /tmp/awx_data_export.json
[root@LXMASTER01 baza]# docker exec -it tools_ascender_1 ascender-manage loaddata awx_data_export.json
CommandError: No fixture named 'awx_data_export' found.
[root@LXMASTER01 baza]#
docker cp awx_data_export.json tools_ascender_1:/tmp/awx_data_export.json
docker exec -it tools_ascender_1 ascender-manage loaddata /tmp/awx_data_export.json


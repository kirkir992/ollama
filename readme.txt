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


[root@PRDADMINOS01 baza]# docker exec -it tools_ascender_1 ascender-manage loaddata /tmp/awx_data_export.json
Traceback (most recent call last):
  File "/usr/local/bin/ascender-manage", line 17, in <module>
    load_entry_point('ascender', 'console_scripts', 'ascender-manage')()
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^
  File "/ascender_devel/ascender/__init__.py", line 133, in manage
    execute_from_command_line(sys.argv)
    ~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/__init__.py", line 443, in execute_from_command_line
    utility.execute()
    ~~~~~~~~~~~~~~~^^
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/__init__.py", line 437, in execute
    self.fetch_command(subcommand).run_from_argv(self.argv)
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/base.py", line 422, in run_from_argv
    self.execute(*args, **cmd_options)
    ~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/base.py", line 466, in execute
    output = self.handle(*args, **options)
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/commands/loaddata.py", line 103, in handle
    self.loaddata(fixture_labels)
    ~~~~~~~~~~~~~^^^^^^^^^^^^^^^^
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/commands/loaddata.py", line 164, in loaddata
    self.load_label(fixture_label)
    ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/management/commands/loaddata.py", line 244, in load_label
    objects = serializers.deserialize(
        ser_fmt,
    ...<3 lines>...
        handle_forward_references=True,
    )
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/serializers/__init__.py", line 146, in deserialize
    return d(stream_or_string, **options)
  File "/var/lib/ascender/venv/ascender/lib64/python3.14/site-packages/django/core/serializers/json.py", line 66, in __init__
    stream_or_string = stream_or_string.read()
MemoryError: Problem installing fixture '/tmp/awx_data_export.json':

docker exec -it awx_task awx-manage dumpdata \
  --indent=2 \
  --exclude=auth.Permission \
  --exclude=contenttypes \
  --exclude=main.activitystream \
  --exclude=main.jobevent \
  --exclude=main.job \
  --exclude=main.unifiedjob \
  --exclude=main.jobhostsummary \
  --exclude=sessions \
  > /u01/awx_only_config.json


docker cp /u01/awx_only_config.json tools_ascender_1:/tmp/awx_only_config.json
docker exec -it tools_ascender_1 ascender-manage loaddata /tmp/awx_only_config.json

docker exec -it tools_postgres_1 psql -U awx -d awx -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"
docker exec -t <nazwa_kontenera_postgres_awx> pg_dumpall -c -U awx > awx_backup.sql

docker exec -i tools_postgres_1 psql -U awx -d awx < /u01/awx_backup.sql

docker exec -it tools_ascender_1 ascender-manage migrate


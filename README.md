# 8.2_Ansible_Playbook

1. Установка Java (тег java, параметры java_jdk_version, java_oracle_jdk_package )
 - Устанавливаем переменную java_home с путём к домашней директории Java
 - скачиваем архив с дистрибутивом с Corntrole на Managed node, должно завершится успехом
 - Создаем домашнюю папку Джава (с правами root)
 - Распаковываем архив(с правами root) с дистрибутивовм в домашнюю папку, каталог с названием архива не создаём, проверяем успех созданием файла {{ java_home }}/bin/java
 - Копируем sh-файл с переменными среды, чтобы они были у всех пользователей


2. Установка Elasticsearch (тег elastic, параметры elastic_version, elastic_home )
 - Скачиваем архив с дистрибутивом с Интернет (3 попытки, сертификат не проверяем, ожидание 60 сек, всегда перекачивать,  должен завершится успехом)
 - Создаем домашнюю папку Эластик (с правами root)
 - Распаковываем архив(с правами root) с дистрибутивовм в домашнюю папку, каталог с названием архива не создаём, проверяем успех созданием файла {{ elastic_home }}/bin/elasticsearch
 - Копируем sh-файл с переменными среды, чтобы они были у всех пользователей


3. Установка Kibana (тег kibana, параметры kibana_version, kibana_home )
 - Скачиваем архив с дистрибутивом с Интернет (3 попытки, сертификат не проверяем, ожидание 60 сек, всегда перекачивать,  должен завершится успехом)
 - Создаем домашнюю папку Кибана (с правами root)
 - Распаковываем архив(с правами root) с дистрибутивовм в домашнюю папку, каталог с названием архива не создаём, проверяем успех созданием файла {{ kibana_home }}/bin/kibana
 - Копируем sh-файл с переменными среды, чтобы они были у всех пользователей (с правами root)
 - Копируем файл конфигурации kibana,  (с правами root), должно завершится успехом
 - Копируем файл демона kibana (с правами root), должно завершится успехом
 - Перезагружаем конфигурацию systemd (с правами root)





#Первый запуск с diff
```shell
root@docker-nm:/home/maxn/ansible/8.2_Ansible_Playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --check

PLAY [Install Java] ******************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Set facts for Java 11 vars] ****************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload .tar.gz file containing binaries from local storage] ********************************************************************************************************************************************
changed: [lolocalhost]

TASK [Ensure installation dir exists] ************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Extract java in the installation directory] ************************************************************************************************************************************************************
fatal: [lolocalhost]: FAILED! => {"changed": false, "msg": "Source '/tmp/jdk-11.0.13.tar.gz' does not exist"}

PLAY RECAP ***************************************************************************************************************************************************************************************************
lolocalhost                : ok=4    changed=1    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   

root@docker-nm:/home/maxn/ansible/8.2_Ansible_Playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Java] ******************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Set facts for Java 11 vars] ****************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload .tar.gz file containing binaries from local storage] ********************************************************************************************************************************************
diff skipped: source file size is greater than 104448
changed: [lolocalhost]

TASK [Ensure installation dir exists] ************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Extract java in the installation directory] ************************************************************************************************************************************************************
changed: [lolocalhost]

TASK [Export environment variables] **************************************************************************************************************************************************************************
--- before
+++ after: /root/.ansible/tmp/ansible-local-1466278ckh4nykb/tmp9w6hauxo/jdk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export JAVA_HOME=/opt/jdk/11.0.13
+export PATH=$PATH:$JAVA_HOME/bin
\ No newline at end of file

changed: [lolocalhost]

PLAY [Install Elasticsearch] *********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload tar.gz Elasticsearch from remote URL] ***********************************************************************************************************************************************************
changed: [lolocalhost]

TASK [Create directrory for Elasticsearch] *******************************************************************************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/elastic/7.10.1",
-    "state": "absent"
+    "state": "directory"
 }

changed: [lolocalhost]

TASK [Extract Elasticsearch in the installation directory] ***************************************************************************************************************************************************
changed: [lolocalhost]

TASK [Set environment Elastic] *******************************************************************************************************************************************************************************
--- before
+++ after: /root/.ansible/tmp/ansible-local-1466278ckh4nykb/tmpjyvmw5v3/elk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export ES_HOME=/opt/elastic/7.10.1
+export PATH=$PATH:$ES_HOME/bin
\ No newline at end of file

changed: [lolocalhost]

PLAY [Install kibana] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload tar.gz  from remote URL] ************************************************************************************************************************************************************************
changed: [lolocalhost]

TASK [Create directrory for kibana] **************************************************************************************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/kibana/7.15.2",
-    "state": "absent"
+    "state": "directory"
 }

changed: [lolocalhost]

TASK [Extract kibana in the installation directory] **********************************************************************************************************************************************************
changed: [lolocalhost]

TASK [Set environment kibana] ********************************************************************************************************************************************************************************
--- before
+++ after: /root/.ansible/tmp/ansible-local-1466278ckh4nykb/tmpui7egtqu/kbn.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export KIBANA_HOME=/opt/kibana/7.15.2
+export PATH=$PATH:$KIBANA_HOME/bin
\ No newline at end of file

changed: [lolocalhost]

TASK [Upload kibana config] **********************************************************************************************************************************************************************************
--- before: /opt/kibana/7.15.2/config/kibana.yml
+++ after: /home/maxn/ansible/8.2_Ansible_Playbook/playbook/files/kibana.yml
@@ -1,115 +1,4 @@
-# Kibana is served by a back end server. This setting specifies the port to use.
-#server.port: 5601
-
-# Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
-# The default is 'localhost', which usually means remote machines will not be able to connect.
-# To allow connections from remote users, set this parameter to a non-loopback address.
-#server.host: "localhost"
-
-# Enables you to specify a path to mount Kibana at if you are running behind a proxy.
-# Use the `server.rewriteBasePath` setting to tell Kibana if it should remove the basePath
-# from requests it receives, and to prevent a deprecation warning at startup.
-# This setting cannot end in a slash.
-#server.basePath: ""
-
-# Specifies whether Kibana should rewrite requests that are prefixed with
-# `server.basePath` or require that they are rewritten by your reverse proxy.
-# This setting was effectively always `false` before Kibana 6.3 and will
-# default to `true` starting in Kibana 7.0.
-#server.rewriteBasePath: false
-
-# Specifies the public URL at which Kibana is available for end users. If
-# `server.basePath` is configured this URL should end with the same basePath.
-#server.publicBaseUrl: ""
-
-# The maximum payload size in bytes for incoming server requests.
-#server.maxPayload: 1048576
-
-# The Kibana server's name.  This is used for display purposes.
-#server.name: "your-hostname"
-
-# The URLs of the Elasticsearch instances to use for all your queries.
-#elasticsearch.hosts: ["http://localhost:9200"]
-
-# Kibana uses an index in Elasticsearch to store saved searches, visualizations and
-# dashboards. Kibana creates a new index if the index doesn't already exist.
-#kibana.index: ".kibana"
-
-# The default application to load.
-#kibana.defaultAppId: "home"
-
-# If your Elasticsearch is protected with basic authentication, these settings provide
-# the username and password that the Kibana server uses to perform maintenance on the Kibana
-# index at startup. Your Kibana users still need to authenticate with Elasticsearch, which
-# is proxied through the Kibana server.
-#elasticsearch.username: "kibana_system"
-#elasticsearch.password: "pass"
-
-# Kibana can also authenticate to Elasticsearch via "service account tokens".
-# If may use this token instead of a username/password.
-# elasticsearch.serviceAccountToken: "my_token"
-
-# Enables SSL and paths to the PEM-format SSL certificate and SSL key files, respectively.
-# These settings enable SSL for outgoing requests from the Kibana server to the browser.
-#server.ssl.enabled: false
-#server.ssl.certificate: /path/to/your/server.crt
-#server.ssl.key: /path/to/your/server.key
-
-# Optional settings that provide the paths to the PEM-format SSL certificate and key files.
-# These files are used to verify the identity of Kibana to Elasticsearch and are required when
-# xpack.security.http.ssl.client_authentication in Elasticsearch is set to required.
-#elasticsearch.ssl.certificate: /path/to/your/client.crt
-#elasticsearch.ssl.key: /path/to/your/client.key
-
-# Optional setting that enables you to specify a path to the PEM file for the certificate
-# authority for your Elasticsearch instance.
-#elasticsearch.ssl.certificateAuthorities: [ "/path/to/your/CA.pem" ]
-
-# To disregard the validity of SSL certificates, change this setting's value to 'none'.
-#elasticsearch.ssl.verificationMode: full
-
-# Time in milliseconds to wait for Elasticsearch to respond to pings. Defaults to the value of
-# the elasticsearch.requestTimeout setting.
-#elasticsearch.pingTimeout: 1500
-
-# Time in milliseconds to wait for responses from the back end or Elasticsearch. This value
-# must be a positive integer.
-#elasticsearch.requestTimeout: 30000
-
-# List of Kibana client-side headers to send to Elasticsearch. To send *no* client-side
-# headers, set this value to [] (an empty list).
-#elasticsearch.requestHeadersWhitelist: [ authorization ]
-
-# Header names and values that are sent to Elasticsearch. Any custom headers cannot be overwritten
-# by client-side headers, regardless of the elasticsearch.requestHeadersWhitelist configuration.
-#elasticsearch.customHeaders: {}
-
-# Time in milliseconds for Elasticsearch to wait for responses from shards. Set to 0 to disable.
-#elasticsearch.shardTimeout: 30000
-
-# Logs queries sent to Elasticsearch. Requires logging.verbose set to true.
-#elasticsearch.logQueries: false
-
-# Specifies the path where Kibana creates the process ID file.
-#pid.file: /run/kibana/kibana.pid
-
-# Enables you to specify a file where Kibana stores log output.
-#logging.dest: stdout
-
-# Set the value of this setting to true to suppress all logging output.
-#logging.silent: false
-
-# Set the value of this setting to true to suppress all logging output other than error messages.
-#logging.quiet: false
-
-# Set the value of this setting to true to log all events, including system usage information
-# and all requests.
-#logging.verbose: false
-
-# Set the interval in milliseconds to sample system and process performance
-# metrics. Minimum is 100ms. Defaults to 5000.
-#ops.interval: 5000
-
-# Specifies locale to be used for all localizable strings, dates and number formats.
-# Supported languages are the following: English - en , by default , Chinese - zh-CN .
-#i18n.locale: "en"
+server.host: 0.0.0.0
+server.port: 5601
+elasticsearch.hosts: ["http://localhost:9200"]
+pid.file: /var/run/kibana/kibana.pid

changed: [lolocalhost]

TASK [Create service file] ***********************************************************************************************************************************************************************************
--- before
+++ after: /root/.ansible/tmp/ansible-local-1466278ckh4nykb/tmpsmf3yjar/kibana.service.j2
@@ -0,0 +1,12 @@
+[Unit]
+Description=Kibana
+After=network.target
+
+[Service]
+ExecStart=/opt/kibana/7.15.2/bin/kibana
+Type=simple
+PIDFile=/var/run/kibana/kibana.pid
+Restart=always
+
+[Install]
+WantedBy=default.target
\ No newline at end of file

changed: [lolocalhost]

TASK [Just force systemd to reread configs] ******************************************************************************************************************************************************************
ok: [lolocalhost]

PLAY RECAP ***************************************************************************************************************************************************************************************************
lolocalhost                : ok=19   changed=13   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   





```

# Второй запуск diff

```shell
root@docker-nm:/home/maxn/ansible/8.2_Ansible_Playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Java] ******************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Set facts for Java 11 vars] ****************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload .tar.gz file containing binaries from local storage] ********************************************************************************************************************************************
ok: [lolocalhost]

TASK [Ensure installation dir exists] ************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Extract java in the installation directory] ************************************************************************************************************************************************************
skipping: [lolocalhost]

TASK [Export environment variables] **************************************************************************************************************************************************************************
ok: [lolocalhost]

PLAY [Install Elasticsearch] *********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload tar.gz Elasticsearch from remote URL] ***********************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Create directrory for Elasticsearch] *******************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Extract Elasticsearch in the installation directory] ***************************************************************************************************************************************************
skipping: [lolocalhost]

TASK [Set environment Elastic] *******************************************************************************************************************************************************************************
ok: [lolocalhost]

PLAY [Install kibana] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload tar.gz  from remote URL] ************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Create directrory for kibana] **************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Extract kibana in the installation directory] **********************************************************************************************************************************************************
skipping: [lolocalhost]

TASK [Set environment kibana] ********************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Upload kibana config] **********************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Create service file] ***********************************************************************************************************************************************************************************
ok: [lolocalhost]

TASK [Just force systemd to reread configs] ******************************************************************************************************************************************************************
ok: [lolocalhost]

PLAY RECAP ***************************************************************************************************************************************************************************************************
lolocalhost                : ok=16   changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0   

```
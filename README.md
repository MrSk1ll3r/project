# project
Проєкт до магістерської дисертації

Для коректної роботи проєкту необіхдно правильно розташувати папки `filebeat` та `elk`, оскільки при неправильному встановленні у вас може статися помилка.
Також вимушений попередити, що проєкт реалізований на ОС `Ubuntu 25.10`, тому врахуйте це при встановленні, 
оскільки на новіших або старіших версіях частина модулів для роботи `Docker, Elasticsearch, Kibana та Filebeat` може потребуватися встановлення або видалення деяких модулів

Для папки `elk` із файлом `docker-compose.yml` розташування не є критичним, але для чистоти експерименту та кращої взаємодії рекомендується розташувати папку `elk` у `/home/user`

Папку ж `filebeat` встановлюємо виключно в системну папку `/etc`, оскільки саме `filebeat.yml` здійснює аналіз самих логів.
Після встановлення уважно перевірте статус файлу filebeat.yml !!! Він має бути під доступом root
У випадку якщо ж файл `filebeat.yml` не є під root, то рекомендується створити цей файл самостійно, вставивши код наприкінці файлу `README`.


Project for the Master’s Thesis

For the project to work correctly, it is necessary to place the `filebeat` and `elk` folders properly, because an incorrect setup may result in an error.
I also need to warn you that the project was implemented on `Ubuntu 25.10`, so please keep this in mind during installation,
since on newer or older versions some modules required for `Docker, Elasticsearch, Kibana, and Filebeat` may need to be installed or removed.

For the `elk` folder containing the `docker-compose.yml` file, the location is not critical; however, for the sake of a clean experiment and better interaction, it is recommended to place the `elk` folder in `/home/user`.

The `filebeat` folder must be installed only in the system directory `/etc`, because `filebeat.yml` performs the analysis of the logs themselves.
After installation, carefully check the status/permissions of the `filebeat.yml` file!!! It must be accessible as root
If `filebeat.yml` is not owned by root, it is recommended to create this file yourself by inserting the code at the end of the `README` file.


###################### MODULES CONFIGURATION (REQUIRED) ########################
filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false

############################# FILEBEAT INPUTS ##################################
filebeat.inputs:
  - type: filestream
    id: system-logs
    enabled: true
    paths:
      - /var/log/syslog
      - /var/log/auth.log
      - /var/log/kern.log
      - /var/log/dpkg.log

############################## FILEBEAT MODULES ################################
filebeat.modules:
  - module: system
    syslog:
      enabled: true
      var.paths: ["/var/log/syslog"]
    auth:
      enabled: true
      var.paths: ["/var/log/auth.log"]

################################ PROCESSORS ####################################
processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
  - add_docker_metadata: ~

########################### OUTPUT TO ELASTICSEARCH ############################
output.elasticsearch:
  hosts: ["http://localhost:9200"]
  protocol: "http"

################################# FILEBEAT LOGGING #############################
logging.to_files: true
logging.files:
  path: /var/log/filebeat
  name: filebeat
  keepfiles: 7
  permissions: 0644

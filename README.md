# Master’s Thesis Project: ELK + Filebeat (Ubuntu 25.10)

Проєкт до магістерської дисертації. Репозиторій містить конфігурацію для розгортання **ELK (Elasticsearch + Kibana)** через **Docker Compose** та налаштування **Filebeat** для збору/відправки логів.

## Зміст

* [Вимоги](#вимоги)
* [Рекомендоване розміщення папок](#рекомендоване-розміщення-папок)
* [Встановлення та запуск](#встановлення-та-запуск)
* [Налаштування Filebeat](#налаштування-filebeat)
* [Перевірка роботи](#перевірка-роботи)

## Вимоги

* **OS:** Ubuntu **25.10** (проєкт тестувався саме на цій версії; на інших версіях можуть бути відмінності в залежностях/пакетах).
* **Docker Engine** та **Docker Compose** (плагін `docker compose` або `docker-compose`).
* Доступ **sudo/root** (потрібен для розміщення й керування конфігом Filebeat у `/etc`).

## Рекомендоване розміщення папок

> Важливо: коректне розташування папок **elk** та **filebeat** критичне для повторюваності експерименту.

### ELK

* Папка `elk` може бути розміщена будь-де, але **рекомендовано**:

  * `/home/user/elk`

### Filebeat

* Папку/конфіг Filebeat потрібно розмістити **в системній директорії**:

  * `/etc/filebeat/`
* Причина: `filebeat.yml` працює з системними логами і зазвичай потребує відповідних прав доступу.

## Встановлення та запуск

### 1) Запуск ELK (Docker Compose)

Перейдіть у директорію з `docker-compose.yml`:

```bash
cd /home/user/elk
```

Запустіть стек:

```bash
sudo docker compose up -d
```

Перевірте, що контейнери піднялись:

```bash
sudo docker ps
```

### 2) Підготовка Filebeat

Переконайтесь, що конфіг лежить тут:

```
/etc/filebeat/filebeat.yml
```

Якщо вам потрібно створити `filebeat.yml` вручну (наприклад, файл має неправильні права або відсутній) — використайте вміст з `new.txt`:

```bash
sudo mkdir -p /etc/filebeat
sudo nano /etc/filebeat/filebeat.yml
# вставте вміст з filebeat/new.txt або new.txt (залежно від вашої структури)
```

## Налаштування Filebeat

### Права доступу (ВАЖЛИВО)

Файл **/etc/filebeat/filebeat.yml** має бути під **root**:

Перевірка:

```bash
ls -l /etc/filebeat/filebeat.yml
```

Виправлення власника/прав (якщо потрібно):

```bash
sudo chown root:root /etc/filebeat/filebeat.yml
sudo chmod 600 /etc/filebeat/filebeat.yml
```

> Примітка: конкретні права можуть залежати від вашого сценарію, але **власник root** — обов’язково.

### Запуск/перезапуск Filebeat

Якщо Filebeat встановлено як сервіс:

```bash
sudo systemctl restart filebeat
sudo systemctl status filebeat --no-pager
```

Якщо запускаєте вручну (рідше):

```bash
sudo filebeat -e -c /etc/filebeat/filebeat.yml
```

## Перевірка роботи

* Перевірте логи Filebeat:

```bash
sudo journalctl -u filebeat -n 100 --no-pager
```

* Перевірте доступність Kibana (типово):

  * `http://localhost:5601`

* Переконайтесь, що індекси/дані з’являються в Elasticsearch/Kibana (Discover / Index Management).

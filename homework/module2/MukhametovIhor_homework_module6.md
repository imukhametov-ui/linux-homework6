# Homework Module 6
ubuntu@ubuntu-VirtualBox:~$ cd ~
nano backup.sh
ubuntu@ubuntu-VirtualBox:~$ chmod +x backup.sh
ubuntu@ubuntu-VirtualBox:~$ mkdir -p ~/test_logs
mkdir -p ~/test_backup
ubuntu@ubuntu-VirtualBox:~$ echo "test log message" > ~/test_logs/app.log
ubuntu@ubuntu-VirtualBox:~$ ./backup.sh ~/test_logs ~/test_backup
Backup created: /home/ubuntu/test_backup/logs_backup_2026-06-22_00-20.tar.gz
ubuntu@ubuntu-VirtualBox:~$ ls -l ~/test_backup
загалом 4
-rw-rw-r-- 1 ubuntu ubuntu 164 чер 22 00:20 logs_backup_2026-06-22_00-20.tar.gz
ubuntu@ubuntu-VirtualBox:~$ ./backup.sh
Usage: ./backup.sh <log_dir> <backup_dir>
ubuntu@ubuntu-VirtualBox:~$ Usage: ./backup.sh <log_dir> <backup_dir>
bash: синтаксична помилка коло неочікуваної лексеми «<»
ubuntu@ubuntu-VirtualBox:~$ ./backup.sh ~/wrong_dir ~/test_backup
Usage: ./backup.sh <log_dir> <backup_dir>
ubuntu@ubuntu-VirtualBox:~$ touch /tmp/backup.lock
./backup.sh ~/test_logs ~/test_backup
rm /tmp/backup.lock
Backup already running
ubuntu@ubuntu-VirtualBox:~$ ^C
ubuntu@ubuntu-VirtualBox:~$
Він перевіряє кількість аргументів, перевіряє існування каталогів, використовує lock-файл `/tmp/backup.lock` для захисту від паралельного запуску, створює архів у форматі `logs_
# Homework Module 6

## Варіант A — Скрипт бекапу логів

### Код скрипта backup.sh

bash
#!/bin/bash

# Check number of arguments
if [ "$#" -ne 2 ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

LOG_DIR="$1"
BACKUP_DIR="$2"
LOCK_FILE="/tmp/backup.lock"

# Check that both arguments are existing directories
if [ ! -d "$LOG_DIR" ] || [ ! -d "$BACKUP_DIR" ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

# Prevent parallel execution
if [ -f "$LOCK_FILE" ]; then
    echo "Backup already running"
    exit 1
fi

touch "$LOCK_FILE"
trap 'rm -f "$LOCK_FILE"' EXIT

DATE=$(date +"%Y-%m-%d_%H-%M")
ARCHIVE="$BACKUP_DIR/logs_backup_$DATE.tar.gz"

tar -czf "$ARCHIVE" -C "$LOG_DIR" .

if [ "$?" -ne 0 ]; then
    echo "Backup failed"
    exit 2
fi

echo "Backup created: $ARCHIVE"

### Перевірка роботи

bash
chmod +x backup.sh
mkdir -p ~/test_logs
mkdir -p ~/test_backup
echo "test log message" > ~/test_logs/app.log
./backup.sh ~/test_logs ~/test_backup
ls -l ~/test_backup
./backup.sh
./backup.sh ~/wrong_dir ~/test_backup
touch /tmp/backup.lock
./backup.sh ~/test_logs ~/test_backup
rm /tmp/backup.lock


### Короткий опис

Скрипт `backup.sh` приймає два аргументи: каталог з логами та каталог для збереження бекапу. 

backup_YYYY-MM-DD_HH-MM.tar.gz` і повідомляє про успішне або помилкове завершення.

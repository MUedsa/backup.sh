# backup.sh

备份由LNMP创建的网站与数据库，并上传备份文件到百度云。

Backup LNMP's websites and mysql databases, then upload backup files to BaiduPCS

## Before this

You need download and install [BaiduPCS-GO](https://github.com/iikira/BaiduPCS-Go/releases/latest).

Choose one right version for you ,and unzip to your dir.

Then login your baidu account.
```
# Maybe you need to give run permission to 'BaiduPCS-Go' .
chmod -X BaiduPCS-Go 
# or
chmod 754 BaiduPCS-Go 
# Warning! You should not give the run permission to other user or group for safety, 
because other can upload your  files in linux to his/her BaiduPCS.

# Login
BaiduPCS-Go login
```

Download or git clone backup.sh script.
```
wget https://github.com/MUedsa/backup.sh/raw/master/backup.sh

# or

git clone https://github.com/MUedsa/backup.sh.git
```

Modify backup.sh
```
vi backup.sh
```

backup.sh
```
BACKUP_SAVE_PATH='/home/backup/'

# if is 0, script will auto backup all dirs in 'BACKUP_DIR_PATH'
IS_BACKUP_ALL_DIR=0
BACKUP_DIR_PATH='/home/wwwroot/'
#else , script will backup dirs in 'BACKUP_DIRS'
BACKUP_DIRS=('/home/wwwroot/default' '/home/wwwroot/yoursite1' '/home/wwwroot/yoursite2')

# mysql cmd path
CMD_MYSQL='/usr/local/mysql/bin/mysql'
# mysqldump cmd path
CMD_MYSQLJUMP='/usr/local/mysql/bin/mysqldump'

# mysql username and password
MYSQL_USERNAME='root'
MYSQL_PASSWORD='password'

# if is 0, script will auto backup all database in your mysql username
IS_BACKUP_ALL_DATABASE=0
# exclude these databases
# +--------------------+
# | Database           |
# +--------------------+
# | information_schema |
# | mysql              |
# | performance_schema |
# | test               |
# +--------------------+
# plase add no-backup databases, not delete these default databases
EXCLUDE_DATABASES=("Database" "mysql" "information_schema" "performance_schema" "test")
# else, script will backup databases  in 'BACKUP_DATABASES'
BACKUP_DATABASES=('yourdatabase1', 'yourdatabase2')

# nginx conf path
NGINX_CONF_PATH='/usr/local/nginx/conf'
# backup files or dirs in NGINX_CONF_PATH, use 'space' separate(if have space in filename , you can like NGINX_BACKUP_CONF_FILES_OR_DIRS="vhost ssl 'muedsa file1' 'muedsa file2'")
NGINX_BACKUP_CONF_FILES_OR_DIRS="vhost ssl"

# before your need to run cut_nginx_log.sh script
IS_CUT_NGINX_LOGS=1
# cut_ngix_logs cmd
CMD_CUT_NGINX_LOGS='bash /home/lnmp1.5/tools/cut_nginx_logs.sh'
# yesterday nginx logs backup path
NGINX_LOGS_PATH="/home/wwwlogs/"$(date -d "yesterday" +"%Y")/$(date -d "yesterday" +"%m")

# if is 0, upload backup files to BaiduPCS
IS_UPLOAD_BAIDUPCS=0
CMD_BAIDUPCS_GO="/home/BaiduPCS-Go/BaiduPCS-Go" # BaiduPCS-Go cmd path
UPLOAD_PACH="/LNMP_BACKUP" # upload to the dir
# if is 0, delete old backup files from BaiduPCS
IS_DELETE_BAIDUPCS_OLD_FILE=1

NEW_BACKUP_FILE_WWW=www-*-$(date +"%Y%m%d").tar.gz
NEW_BACKUP_FILE_SQL=db-*-$(date +"%Y%m%d").sql
NEW_BACKUP_FILE_NGINX_CFGS=nginx-cfgs-$(date +"%Y%m%d").tar.gz
NEW_BACKUP_FILE_NGINX_LOGS=nginx-logs-$(date +"%Y%m%d").tar.gz
OLD_BACKUP_FILE_WWW=www-*-$(date -d -3day +"%Y%m%d").tar.gz
OLD_BACKUP_FILE_SQL=db-*-$(date -d -3day +"%Y%m%d").sql
OLD_BACKUP_FILE_NGINX_CFGS=nginx-cfgs-$(date -d -3day +"%Y%m%d").tar.gz
OLD_BACKUP_FILE_NGINX_LOGS=nginx-logs-$(date -d -3day +"%Y%m%d").tar.gz
```

You must check and modify the values in 'backup.sh'
```
# mysql cmd path
CMD_MYSQL='/usr/local/mysql/bin/mysql'
# mysqldump cmd path
CMD_MYSQLJUMP='/usr/local/mysql/bin/mysqldump'

# mysql username and password
MYSQL_USERNAME='root'
MYSQL_PASSWORD='password' 

CMD_BAIDUPCS_GO="/root/BaiduPCS-Go/BaiduPCS-Go" # BaiduPCS-Go cmd path
UPLOAD_PACH="/LNMP_BACKUP" # upload to the dir
```

## Run
```
chmod -X backup.sh
bash backup.sh
```

## Cron
```
# Ubuntu
corntab -e
# select editor and add:
0  0 * * * bash /root/backup.sh > /home/backup/log/$(date +"\%Y\%m\%d-\%H\%M\%S").log 2>&1
# reload corn
service cron reload
```

## Update

### 20180908
- fix: a stupid mistake in 'cut_nginx_logs.sh'

### 20180907
- feature: nginx cfg backup
- fix: cron cmd error in README.md
- feature: cut and backup nginx logs
- fix: I unkown array's start from index=0 or 1, but I modify i=0

### 20180906 
- feature: backup website files and datebases
- feature: upload backup files to BaiduPCS
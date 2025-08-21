# Frappe-ERPNext Version-15 in Ubuntu 22.04 LTS
Cài đặt ERPNext 15 trên Ubuntu từ phiên bản 22.04 trở lên

#### Refer this for default python 3.11 setup


### Yêu cầu 

      Python 3.11+
      Node.js 18+
      
      Redis 5                                       (caching and real time updates)
      MariaDB 10.3.x / Postgres 9.5.x               (to run database driven apps)
      yarn 1.12+                                    (js dependency manager)
      pip 20+                                       (py dependency manager)
      wkhtmltopdf (version 0.12.5 with patched qt)  (for pdf generation)
      cron                                          (bench's scheduled jobs: automated certificate renewal, scheduled backups)
      NGINX                                         (proxying multitenant sites in production)


------
### Cài đặt python 3.11.xx
------

> ## `Chú ý: Nếu phiên bản Ubuntu từ 23.xx trở lên thì không cần cài đặt python 3.11.x `
    
#### Thêm gói thư viện vào hệ thống.

      sudo add-apt-repository ppa:deadsnakes/ppa -y
      sudo apt update
      
#### Cài đặt python 3.11:

      sudo apt install python3.11
      python3.11 --version

    
#### Giải nén và cài đặt pyhton3.11.

      sudo apt install python3.11-full



#### Refer this for default python 3.11 setup

- [www.linuxcapable.com](https://www.linuxcapable.com/how-to-install-python-3-11-on-ubuntu-linux/#google_vignette)
- [ubuntuhandbook.org](https://ubuntuhandbook.org/index.php/2022/10/python-3-11-released-how-install-ubuntu)

-----


### STEP 1 Cài đặt git
    sudo apt-get install git

### STEP 2 cài đặt python-dev

    sudo apt-get install python3-dev

### STEP 3 cài đặt setuptools và pip (Python's Package Manager).

    sudo apt-get install python3-setuptools python3-pip

### STEP 4 cài đặt virtualenv
    
    sudo apt install python3.11-venv
    

### STEP 5 Cài đặt MariaDB

    sudo apt-get install software-properties-common
    sudo apt install mariadb-server
    sudo mysql_secure_installation
    
    
      In order to log into MariaDB to secure it, we'll need the current
      password for the root user. If you've just installed MariaDB, and
      haven't set the root password yet, you should just press enter here.

      Enter current password for root (enter for none): # PRESS ENTER
      OK, successfully used password, moving on...
      
      
      Switch to unix_socket authentication [Y/n] Y
      Enabled successfully!
      Reloading privilege tables..
       ... Success!
 
      Change the root password? [Y/n] Y
      New password: 
      Re-enter new password: 
      Password updated successfully!
      Reloading privilege tables..
       ... Success!

      Remove anonymous users? [Y/n] Y
       ... Success!
 
       Disallow root login remotely? [Y/n] Y
       ... Success!

       Remove test database and access to it? [Y/n] Y
       - Dropping test database...
       ... Success!
       - Removing privileges on test database...
       ... Success!
 
       Reload privilege tables now? [Y/n] Y
       ... Success!

 
    
    
    
### STEP 6  Cài đặt MySQL database development files

    sudo apt-get install libmysqlclient-dev

### STEP 7 Sửa file mariadb configuration ( unicode character encoding )

    sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

thêm dòng bên dưới vào file 50-server.cnf

    
    [server]
    user = mysql
    pid-file = /run/mysqld/mysqld.pid
    socket = /run/mysqld/mysqld.sock
    basedir = /usr
    datadir = /var/lib/mysql
    tmpdir = /tmp
    lc-messages-dir = /usr/share/mysql
    bind-address = 127.0.0.1
    query_cache_size = 16M
    log_error = /var/log/mysql/error.log
    
    [mysqld]
    innodb-file-format=barracuda
    innodb-file-per-table=1
    innodb-large-prefix=1
    character-set-client-handshake = FALSE
    character-set-server = utf8mb4
    collation-server = utf8mb4_unicode_ci      
     
    [mysql]
    default-character-set = utf8mb4

Now press (Ctrl-X) to exit

    sudo service mysql restart

### STEP 8 Cài đặt Redis
    
    sudo apt-get install redis-server

### STEP 9 Cài đặt Node.js 18.X package

    sudo apt install curl 
    curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
    source ~/.profile
    nvm install 18

### STEP 10  Cài đặt Yarn

    sudo apt-get install npm

    sudo npm install -g yarn

### STEP 11 Cài đặt wkhtmltopdf

    sudo apt-get install xvfb libfontconfig wkhtmltopdf
    
### Tạo user và cấp quyền
      sudo adduser frappe
      sudo usermod -aG sudo frappe
      
      su - frappe  # Chuyển sang user frappe
### STEP 12 Cài đặt frappe-bench

    sudo -H pip3 install frappe-bench
    
    bench --version
    
### STEP 13 initilise the frappe bench & install frappe latest version 

    bench init frappe-bench --frappe-branch version-15 --python python3.11
    
    cd frappe-bench/
### Change user directory permissions
This will give the bench user execution permission to the home directory.

      chmod -R o+rx /home/[frappe-user]
    bench start
    
### STEP 14 tạo site trong frappe bench 
    
    bench new-site tên-site-của-bạn (nguyentrunghau.name.vn)
    
    bench --site tên-site-của-bạn add-to-hosts (trỏ về localhost)

Mở url http://tên-site-của-bạn:8000 để đăng nhập 


### STEP 15 Cài đặt ERPNext phiên bản 15 trong bench & site

    
    bench get-app erpnext --branch version-15
    ###OR
    bench get-app https://github.com/frappe/erpnext --branch version-15

    bench --site dcode.com install-app erpnext
    
    bench start
    
    #### Lưu ý:
    Phiên bản ERPNext 15 trở lên muốn sử dụng ip cục bộ trong mạng LAN thì phải sử dụng lệnh

    bench use tên-site-của-bạn

    sau đó chạy lệnh
    bench start
### Note
      Sau khi update hoặc upgrade lên phiên bản mới có thể Frappe sẽ không khởi chạy được lúc đó chúng ta sẽ chỉnh sửa 
      sudo nano /etc/redis/redis.conf
      đổi cổng sang cổng khác như 15000
      sau đó chạy lệnh
      sudo systemctl restart redis.service
### Xử lý lỗi ERROR: sudo supervisorctl restart frappe: subprocess.CalledProcessError: Command 'sudo supervisorctl restart frappe:' returned non-zero exit status 2.
      1. Kiểm tra cấu hình Supervisor
            sudo supervisorctl status
      2. Kiểm tra file cấu hình Supervisor
            ls /etc/supervisor/conf.d/
      Nếu không có file nào liên quan đến , bạn có thể tạo lại bằng lệnh:
            bench setup supervisor
            sudo ln -s ~/frappe-bench/config/supervisor.conf /etc/supervisor/conf.d/frappe.conf
            sudo supervisorctl reread
            sudo supervisorctl update
    3. Khởi động lại Supervisor
          sudo service supervisor restart
          hoặc
         sudo systemctl restart supervisor
   4. Thử lại lệnh khởi động tiến trình
           sudo supervisorctl restart all
### Xử lý lỗi frappe-bench-redis:frappe-bench-redis-cache: ERROR (spawn error) frappe-bench-redis:frappe-bench-redis-queue: ERROR (spawn error)
   1. Thiết lập lại Supervisor
      bench setup supervisor
      sudo ln -s ~/frappe-bench/config/supervisor.conf /etc/supervisor/conf.d/frappe-bench.conf
      sudo supervisorctl reread
      sudo supervisorctl update
   2. Khởi động lại toàn bộ tiến trình
      bench restart
### Xử lý lỗi sudo cat /etc/supervisor/conf.d/frappe-bench.conf cat: /etc/supervisor/conf.d/frappe-bench.conf: No such file or directory
   1. Tạo lại file cấu hình Supervisor
      bench setup supervisor
   2. Liên kết file cấu hình vào thư mục Supervisor
      sudo ln -s ~/frappe-bench/config/supervisor.conf /etc/supervisor/conf.d/frappe-bench.conf
   3. Tải lại cấu hình Supervisor
      sudo supervisorctl reread
      sudo supervisorctl update
   4. Khởi động lại Supervisor
      sudo service supervisor restart
      hoặc
      sudo systemctl restart supervisor
   5. Khởi động lại các tiến trình ERPNext
      bench restart

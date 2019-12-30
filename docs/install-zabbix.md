# Zabbix 
  

## Các bước cài đặt Zabbix
### 1. Cái đặt LAMP 

#### 1.1 : Cài đặt Apache
```
# Install apache 
sudo yum -y install httpd vim 
 
# Config apache
vi /etc/httpd/conf/httpd.conf 
# grep ServerName /etc/httpd/conf/httpd.conf 
ServerName <DNS name or IP address>

# grep ServerAdmin /etc/httpd/conf/httpd.conf 
ServerAdmin root@localhost

    
# Add Prod ServerTokens 
ServerSignature Off
ServerTokens Prod

# Restart apache
sudo systemctl restart httpd

```
#### 1.2: Cài đặt MariaDB
```
# Remove mariadb
sudo yum remove mariadb-server

# Create install repos for mariadb
cat <<EOF | sudo tee /etc/yum.repos.d/MariaDB.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
EOF

# Enable cache
sudo yum makecache fast

# Install mariadb server, client
sudo yum -y install MariaDB-server MariaDB-client

# Enable mariadb service
sudo systemctl enable --now mariadb

# Start mysql server deamon
sudo systemctl restart mysqld

```
#### 1.3: Cài đặt LAMP -PHP
```
# Install PHP with required for zabbix
sudo yum -y install php php-pear php-cgi php-common php-mbstring php-snmp php-gd php-xml php-mysql php-gettext php-bcmath

# Set front-end timezone
sudo sed -i "s/^;date.timezone =$/date.timezone = \"Africa\/Nairobi\"/" /etc/php.ini

# Restart to apply config
sudo systemctl restart httpd

```
### 2. Cài đặt Zabbix Server

#### 2.1 Thêm repo và cài đặt Zabbix
```
# Adding Zabbix repository
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm

# Server/proxy/frontend installation
# To install Zabbix server (available for RHEL 7, deprecated on RHEL 6) with MySQL support:
yum install zabbix-server-mysql

# To install Zabbix proxy with MySQL support:
yum install zabbix-proxy-mysql

# To install Zabbix frontend (available for RHEL 7, deprecated on RHEL 6) with MySQL support:
yum install zabbix-web-mysql

```
#### 2.2 Thêm DataBase
```
# Creating database
export zabbix_db_pass="StrongPassword"
mysql -uroot -p <<MYSQL_SCRIPT
create database zabbix;
grant all privileges on zabbix.* to zabbix@'localhost' identified by '${zabbix_db_pass}';
FLUSH PRIVILEGES;
MYSQL_SCRIPT

# Importing data
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix

```
#### 2.3 Config Zabbix Server
```
# Configure database for Zabbix server/proxy
# Edit zabbix_server.conf (and zabbix_proxy.conf) to use their respective databases. For example:
vim /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=<password>

# Starting Zabbix server process
# It's time to start Zabbix server process:
sudo systemctl start zabbix-server

# and make it start at system boot:
sudo systemctl enable zabbix-server

```
#### 2.4 Config Zabbix frontend
```
vim /etc/httpd/conf.d/zabbix.conf
php_value max_execution_time 300
php_value memory_limit 128M
php_value post_max_size 16M
php_value upload_max_filesize 2M
php_value max_input_time 300
php_value max_input_vars 10000
php_value always_populate_raw_post_data -1
php_value date.timezone Europe/Riga

```
#### 2.5 Config SELinux và Firewall
```
# SELinux configuration
# Having SELinux status enabled in enforcing mode, you need to execute the following commands
# to enable communication between Zabbix frontend and server:
setsebool -P httpd_can_connect_zabbix on
setsebool -P httpd_can_network_connect_db on
sudo systemctl restart httpd

# If you have ufw firewall installed and running on your system, ensure you allow port 5000 and port 5001
sudo firewall-cmd --permanent --add-port=10050/tcp
sudo firewall-cmd --permanent --add-port=10051/tcp
sudo firewall-cmd --reload

# Disable firewall if you not have connect to zabbix website
sudo systemctl stop firewalld

# restart http server deamon
sudo systemctl restart httpd

```

### 3. Cài đặt Zabbix Agent 

#### 3.1 Zabbix Agent trên CentOS 7 
```
# Add Zabbix Agent repository
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
# Install Zabbix Agent
yum install -y zabbix-agent
yum install -y zabbix-get
# Configure Zabbix Agent
vim /etc/zabbix/zabbix_agentd.conf
# Zabbix Server IP Address or Hostname
Server=<Server IP or DNS name>
# Specify Zabbix's server IP address
ServerActive=<Server IP or DNS name>
# Client Hostname
Hostname=<Agent host name or IP address>
# Start zabbix agent
service zabbix-agent start
# Enable zabbix-agent to start automatically on system boot.
systemctl enable zabbix-agent

```
#### 3.2 Zabbix Agent trên Ubuntu 18.04
```
# Add Zabbix Agent repository
wget https://repo.zabbix.com/zabbix/4.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_4.0-2+bionic_all.deb
dpkg -i zabbix-release_4.0-2+bionic_all.deb
apt update
# Install Zabbix Agent
apt-get install -y zabbix-agent
apt-get install -y zabbix-get
# Configure Zabbix Agent
vim /etc/zabbix/zabbix_agentd.conf
# Zabbix Server IP Address or Hostname
Server=<Server IP or DNS name>
# Specify Zabbix's server IP address
ServerActive=<Server IP or DNS name>
# Client Hostname
Hostname=<Agent host name or IP address>
# Start zabbix agent
service zabbix-agent start
# Enable zabbix-agent to start automatically on system boot.
update-rc.d zabbix-agent enable

```
  



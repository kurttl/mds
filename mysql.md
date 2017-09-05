# Setup mysql/phpmyadmin
## mysql
[install mysql](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-16-04)  
[setup gerrit mysql db 1](http://soarlin.pixnet.net/blog/post/31408865-%5B%E7%AD%86%E8%A8%98%5D-ubuntu%E4%B8%8A%E6%9E%B6%E8%A8%ADgerrit)  
[setup gerrit mysql db 2](https://review.openstack.org/Documentation/install.html#createdb_mysql)  
```
$ sudo apt-get install mysql-server
$ sudo apt-get install mysql-client
```
```
  $ mysql

  CREATE USER 'gerrit2'@'localhost' IDENTIFIED BY 'secret';
  CREATE DATABASE reviewdb;
  GRANT ALL ON reviewdb.* TO 'gerrit2'@'localhost';
  FLUSH PRIVILEGES;
```

## phpmyadmin
[install phpmyadmin](http://www.itread01.com/content/1496935203.html)  
Install MySQL  
```
$ sudo apt-get install mysql-server
$ sudo apt-get install mysql-client
```
Install phpmyadmin  
```
$ sudo apt-get install phpmyadmin
$ sudo apt-get install php-mbstring
$ sudo apt-get install php-gettext
安裝時選擇自動配置數據庫，輸入數據庫root賬號的密碼
如果不安裝以上兩個php軟件包，則會報錯或者白屏，提示找不到/usr/share/php/php-gettext/gettext.inc之類的錯誤
```
Create /var/www/html/phpmyadmin   
```
$ sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
$ cd /var/www/html/phpmyadmin
$ sudo cp config.sample.inc.php config.inc.php
```
Edit config.inc.php   
```
$ sudo vim /usr/share/phpmyadmin/config.inc.php
	$i++;
	$cfg['Servers'][$i]['auth_type'] = 'cookie';
	$cfg['Servers'][$i]['host'] = 'localhost';
	$cfg['Servers'][$i]['connect_type'] = 'tcp';
	$cfg['Servers'][$i]['compress'] = false;
	$cfg['Servers'][$i]['AllowNoPassword'] = false;

	$i++;
	$cfg['Servers'][$i]['auth_type'] = 'cookie';
	$cfg['Servers'][$i]['host'] = 'handy-rom.csojfhhlojxj.ap-southeast-1.rds.amazonaws.com:3306';
	$cfg['Servers'][$i]['connect_type'] = 'tcp';
	$cfg['Servers'][$i]['compress'] = false;
	$cfg['Servers'][$i]['AllowNoPassword'] = false;

$ sudo vim /etc/phpmyadmin/config.inc.php
	if (check_file_access('/usr/share/phpmyadmin/config.inc.php')) {
    	require('/usr/share/phpmyadmin/config.inc.php');
	}
```
Modify php config   
```
$ sudo vim /etc/php/7.0/apache2/php.ini
display_errors = On(顯示錯誤日誌，出現兩次，都要改，不然無效)
extension=php_mbstring.dll (開啟mbstring)
```
Restart apache   
```
$ sudo vim /etc/apache2/ports.conf
$ sudo /etc/init.d/apache2 status
$ sudo /etc/init.d/apache2 restart
```
Test http://localhost/phpmyadmin/
Test http://52.77.179.161:4043/phpmyadmin/
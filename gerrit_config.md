# Gerrit Config

## Enable github oauth

#### Setup GitHub OAuth in your Github account
1. [Steps](https://github.com/davido/gerrit-oauth-provider/wiki/Getting-Started)
2. [Setup an new application](https://github.com/settings/applications/new)

#### Install plugin gerrit-oauth-provider.jar
1. Build this plugin by yourself: [link](https://github.com/davido/gerrit-oauth-provider#build)
2. Or download prebuilt binary: [link](https://github.com/davido/gerrit-oauth-provider/releases)
3. Install new plugin: [link](https://github.com/davido/gerrit-oauth-provider#install)
3. Confige gerrit.config: [link](https://github.com/davido/gerrit-oauth-provider/blob/master/src/main/resources/Documentation/config.md)
```
$ vim ~/review_site/etc/gerrit.config
[auth]
--type = OPENID
--trustedOpenID = ^.*$
[plugin "gerrit-oauth-provider-github-oauth"]
--client-id = xxxxxxxx
--client-secret = xxxxxxxxxxxxxxxxxxxxxxxxxx
```

#### User login Gerrit via Github
1. Login https://github.com/ first
2. Go to https://gerrit.xxx.xxx/
3. Click sign in on the up right corner
4. Click GitHub OAuth2
5. Click your name on the up right corner
6. Click Settings
7. Change username which is used for “git push” and “repo init”
8. Click Contact Information > Register New Email to add your email if it's empty
Click SSH Public Keys
9. Add key just the same thing you did on github.com


## Setup Google SMTP
#### Edit gerrit.config
```
$ vim ~/review_site/etc/gerrit.config
[sendemail]
--smtpServer = smtp.gmail.com
--smtpServerPort = 465
--smtpEncryption = SSL
--smtpUser = xxx.xxx@xxx.com
--smtpPass = xxxxxxx
```
#### Allow the SMTP permission on your Google mail account
https://myaccount.google.com/lesssecureapps

## Install phppgadmin
[link](https://www.howtoforge.com/tutorial/ubuntu-postgresql-installation/)   
#### Install
```
$ sudo apt-get -y install postgresql postgresql-contrib phppgadmin
```
#### config apache2
```
$ sudo vim /etc/apache2/conf-available/phppgadmin.conf
	# Only allow connections from localhost:
	#Require local
	allow from all
$ sudo vim /etc/apache2/ports.conf
	Listen 4000
```
#### config phppgadmin
```
$ sudo vim /etc/phppgadmin/config.inc.php
	$conf['extra_login_security'] = false;
```
#### Restart PostgreSQL and Apache2
```
$ sudo systemctl enable postgresql
$ sudo systemctl enable apache2
$ sudo systemctl restart postgresql
$ sudo systemctl restart apache2
```
#### Test http://rom-gerrit-raw.handy.travel:4000/phppgadmin/

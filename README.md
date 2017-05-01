# dubbo
dubbo-serve

端口规划：

svn 9000  admin/admin

sonarqube 9090  admin/admin

nexus 8081  admin/admin123

zookeeper 8080

hudson 8082  admin/admin(装在性能较好机器)

服务启动：
service httpd start

jdk版本：1.7

消费者：只有在访问页面的情况下才注册到zookeeper注册中心

注意点

1.edu-service-user报错java.lang.ClassNotFoundException: org.springframework.web.context.ContextLoaderListener
右击工程，选中Build Path -> Deployment Assembly -> Add -> Java Build Path Entries -> Maven Dependencies

2.spring-mybatis.xml基于Druid数据库链接池的数据源配置,解密密码必须要配置的项需注释

3.在CentOS/RHEL 7上开启端口

#即时打开，这里也可以是一个端口范围，如1000-2000/tcp

firewall-cmd --add-port=2181/tcp

firewall-cmd --add-port=2888/tcp

firewall-cmd --add-port=3888/tcp

#写入配置文件

firewall-cmd --permanent --add-port=2181/tcp

firewall-cmd --permanent --add-port=2888/tcp

firewall-cmd --permanent --add-port=3888/tcp


#重启防火墙
firewall-cmd --reload 

firewall-cmd --zone=public --add-port=2181/tcp --permanent

firewall-cmd --zone=public --add-port=2888/tcp --permanent

firewall-cmd --zone=public --add-port=3888/tcp --permanent

firewall-cmd --reload 

查看已经开放的端口：

firewall-cmd --list-ports

4.mysql远程授权问题

Mac用brew安装mysql，解决远程连接授权问题

设置远程访问：

(1)、mysql -u root -p

(2)、grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;

flush privileges;

(3)、打开/usr/local/Cellar/mysql/5.7.16/下的homebrew.mxcl.mysql.plist文件，去掉

--bind-address=127.0.0.1

(4)、brew services stop mysql

brew services start mysql

5.安装svn遇到困难

每次启动httpd报以下错误：
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.

错误原因：

修改/etc/httpd/conf.d/subversion.conf(没有则新建)，內容为
忘记加LoadModule dav_svn_module     modules/mod_dav_svn.so
LoadModule authz_svn_module   modules/mod_authz_svn.so
这两行

subversion.conf完整内容

#必须
LoadModule dav_svn_module     modules/mod_dav_svn.so    
#必须
LoadModule authz_svn_module   modules/mod_authz_svn.so
<Location /repo>
	DAV svn
	SVNListParentPath on
	SVNParentPath /svn
	AuthType Basic
	Satisfy Any
	AuthName "Subversion repositories"
    AuthUserFile /svn/passwd.http       
    AuthzSVNAccessFile /svn/authz 
	Require valid-user
</Location>
RedirectMatch ^(/svn)$ $1/

4.sonarqube集成出现问题

eclipse:clean install sonar:sonar

[WARNING] The requested profile "pom.xml" could not be activated because it does not exist.
[ERROR] Failed to execute goal org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar (default-cli) on project edu-common: SCM provider autodetection failed. Both git and svn claim to support this project. Please use sonar.scm.provider to define SCM of your project. -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException

解决方案：登录sonarqube管理，找到SCM，设置为true

[ERROR] SonarQube server [http://localhost:9090/sonarqube] can not be reached
解决方案：登录sonarqube管理，找到SCM，设置为true
System Configurations -> Sonar  
Server URL	http://192.168.1.110:9090/sonarqube


本文介绍了笔者在使用腾讯云过程中，布置Jenkins的时候遇到的一些坑。文章的亮点在于版本锁定。根据笔者提供的步骤和代码，你也能够顺利的在centos上搭建起Jenkins服务。

# 选择放置下载安装包的位置
cd /root
# 下载版本号为2.190.3-1.1的jenkins
wget https://repo.huaweicloud.com/jenkins/redhat-stable/jenkins-2.190.3-1.1.noarch.rpm 
rpm -ivh jenkins-2.190.3-1.1.noarch.rpm
# 修改配置文件
vi /etc/sysconfig/jenkins
# 这是修改的配置文件中需要改变的内容
JENKINS_USER="root"
JENKINS_PORT="9999"
# 开启防火墙
systemctl start firewalld
# 开放9999端口
firewall-cmd --add-port=9999/tcp --permanent
# 重启防火墙
firewall-cmd --reload
# 启动jenkins
systemctl start jenkins
# 查看服务状态
service jenkins status
# 另一种启动方式
/etc/init.d/jenkins restart
# 查看初次登录的时候的密码
cat /var/lib/jenkins/secrets/initialAdminPassword
# 不要安装任何插件
# 创建管理员账户
# 首先进入插件管理中点击available plugins下载插件清单（Jenkins->Manage Jenkins->Manage Plugins，点击Available）
# 在linux服务器中查看插件清单文件是否已经下载
cd /var/lib/jenkins/updates
ls # 查看是否有default.json这个文件
# 替换默认镜像源
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json && sed -i 's/https:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
# 在jenkins控制台中
Manage Plugins --> Advanced --> Update Site --> https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
# 重启jenkins
http://${ip}:9999/restart
# 汉化
Jenkins->Manage Jenkins->Manage Plugins->Available->"Chinese"
# 用户权限管理
Jenkins->Manage Jenkins->Manage Plugins->Available->"Role-based Authorization Strategy"
# jenkins拥有调用其他服务的凭证管理
Jenkins->Manage Jenkins->Manage Plugins->Available->"Credentials Binding"


---
---
---

# 升级jenkins以使用插件
rpm -qa|grep jenkins
rpm -e --nodeps jenkins*
cd /root
wget https://repo.huaweicloud.com/jenkins/redhat-stable/jenkins-2.361.1-1.1.noarch.rpm 
rpm -ivh jenkins-2.361.1-1.1.noarch.rpm
# 如果/etc/init.d/jenkins文件存在，则安装成功
# 配置文件
sudo vi /etc/init.d/jenkins
#
	candidates="
	/etc/alternatives/java
	/usr/lib/jvm/java-11.0/bin/java # 这里将java1.8相关删除了
	/usr/lib/jvm/jre-11.0/bin/java
	/usr/lib/jvm/java-11-openjdk-amd64
	/usr/bin/java  #或者替换这里
	"
#
# 修改配置文件
vi /etc/sysconfig/jenkins
# 这是修改的配置文件中需要改变的内容
JENKINS_USER="root"
JENKINS_PORT="9999"
# 开启防火墙
systemctl start firewalld
# 开放9999端口
firewall-cmd --add-port=9999/tcp --permanent
# 重启防火墙
firewall-cmd --reload
# 启动
# 必须是这种方式
cd /etc/init.d
./jenkins start
# 查看初次登录的时候的密码
cat /var/lib/jenkins/secrets/initialAdminPassword
# 不要安装任何插件
# 创建管理员账户
# 首先进入插件管理中点击available plugins下载插件清单（Jenkins->Manage Jenkins->Manage Plugins，点击Available）
# 在linux服务器中查看插件清单文件是否已经下载
cd /var/lib/jenkins/updates
ls # 查看是否有default.json这个文件
# 替换默认镜像源
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json && sed -i 's/https:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
# 在jenkins控制台中
Manage Plugins --> Advanced --> Update Site --> https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
# 重启jenkins
http://${ip}:9999/restart
# 汉化
Jenkins->Manage Jenkins->Manage Plugins->Available->"Chinese"
Jenkins->Manage Jenkins->Manage Plugins->Available->"Locale"
Jenkins->Manage Jenkins->Manage Plugins->Available->"Localization:Chinese"
# 用户权限管理
Jenkins->Manage Jenkins->Manage Plugins->Available->"Role-based Authorization Strategy"
# jenkins拥有调用其他服务的凭证管理
Jenkins->Manage Jenkins->Manage Plugins->Available->"Credentials Binding"
# 安装Git插件
Jenkins->Manage Jenkins->Manage Plugins->Available->"Git"
# 重启jenkins
http://${ip}:9999/restart
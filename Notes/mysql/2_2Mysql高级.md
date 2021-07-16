# 2、高级部分

1、Linux系统安装mysql

- ```web-idl
  下载地址：https://dev.mysql.com/downloads/mysql/5.7.html#downloads
  ```

- ```
  //直接安装
  1). 卸载 centos 中预安装的 mysql
  	rpm -qa | grep -i mysql
  	rpm -e mysql-libs-5.1.71-1.el6.x86_64 --nodeps 
  2). 上传 mysql 的安装包  
  	alt + p -------> put E:/test/MySQL-5.6.22-1.el6.i686.rpm-bundle.tar 
  3). 解压 mysql 的安装包   
  	mkdir mysql  
  	tar -xvf MySQL-5.6.22-1.el6.i686.rpm-bundle.tar -C /root/mysql 
  4). 安装依赖包   
  	yum -y install libaio.so.1 libgcc_s.so.1 libstdc++.so.6 libncurses.so.5 --setopt=protected_multilib=false  
  	yum update libstdc++-4.4.7-4.el6.x86_64 
  5). 安装 mysql-client  
  	rpm -ivh MySQL-client-5.6.22-1.el6.i686.rpm 
  6). 安装 mysql-server  
  	rpm -ivh MySQL-server-5.6.22-1.el6.i686.rpm
  	
  	service mysql start 
  	service mysql stop 
  	service mysql status 
  	service mysql restart
  	
  	授权远程访问 :  grant all privileges on *.* to 'root' @'%' identified by 'itcast';flush privileges;
  	
  ```

- docker安装

  - 安装`yum-utils`：

    ```
    yum install -y yum-utils device-mapper-persistent-data lvm2
    ```

  - 为yum源添加docker仓库位置：

    ```
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```

  - 安装docker

    ```
    yum install docker-ce
    ```

  - 启动docker：

    ```
    systemctl start docker
    ```

- mysql安装

  - 下载MySQL`5.7`的docker镜像：

    - ```
      docker pull mysql:5.7
      ```

  - 使用如下命令启动MySQL服务：

    - ```
      docker run -p 3306:3306 --name mysql \
      -v /mydata/mysql/log:/var/log/mysql \
      -v /mydata/mysql/data:/var/lib/mysql \
      -v /mydata/mysql/conf:/etc/mysql \
      -e MYSQL_ROOT_PASSWORD=root  \
      -d mysql:5.7
      
      -p 3306:3306：将容器的3306端口映射到主机的3306端口
      -v /mydata/mysql/conf:/etc/mysql：将配置文件夹挂在到主机
      -v /mydata/mysql/log:/var/log/mysql：将日志文件夹挂载到主机
      -v /mydata/mysql/data:/var/lib/mysql/：将数据文件夹挂载到主机
      -e MYSQL_ROOT_PASSWORD=root：初始化root用户的密码
      ```

  - 进入运行MySQL的docker容器：、

    - ```
      docker exec -it mysql /bin/bash
      ```

  - 使用MySQL命令打开客户端：

    - ```
      mysql -uroot -proot --default-character-set=utf8
      ```

  - 创建数据库：

    - ```
      create database mall character set utf8
      ```

  - 安装上传下载插件，并将`document/sql/mall.sql`上传到Linux服务器上：

    - ```
      yum -y install lrzsz
      ```

  - 将`mall.sql`文件拷贝到mysql容器的`/`目录下：

    - ```bash
      docker cp /mydata/mall.sql mysql:/
      ```

  - 将sql文件导入到数据库：

    - ```bash
      use mall;
      source /mall.sql;
      ```

  - 创建一个`reader:123456`帐号并修改权限，使得任何ip都能访问：

    - ```sql
      grant all privileges on *.* to 'reader' @'%' identified by '123456';
      ```

  
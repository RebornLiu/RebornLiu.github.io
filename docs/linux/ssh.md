## ubuntu安装openssh并远程连接

1. 安装openssh-server

   ```bash
   sudo apt-get install openssh-server
   ```

   注意安装完成时会有一个配置提示

2. 设置sshd_config

   ```bash
   sudo vim /etc/ssh/sshd_config 
   
   #设置允许远端ssh的root登录
   PermitRootLogin yes
   ```

3. 设置ssh_config，注意和第二部的文件不是同一个

   ```bash
   
   ```

4. 查看本地防火墙 如果存在关闭

5. 将ssh添加到开机启动任务

   ```bash
   sudo update-rc.d ssh defaults
   ```

6. ssh命令

   ```bash
   #启动
   sudo service sshd start
   #停止服务
   sudo service sshd stop
   #重启
   sudo service sshd restart
   #查看服务状态  Active: active (running)表示运行
   sudo service ssh status
   ```

7. 使用ssh客户端登录




# Ansible教程

![](E:\文档\Ansible\TIM图片20180828205622.jpg)

#### 优势：

- 相对于 puppet 和 saltstack，ansible 无需客户端，更轻量级。
- 甚至都不用启动服务
- 更强的远程命令执行操作
- 不输于 puppet 和 saltstack 的其他优势

#### 安装：

- 配置好 EPEL 源

  ```
  # yum -y install epel-release
  ```

- yum 安装

  ```
  # yum -y install ansible
  ```

  确认安装版本

  ```
  # ansible --version
  ```

- ssh认证

  ```
  # ssh-keygen                  #在控制节点上操作，一路回车
  # ssh-copy-id -i 192.168.1.190   #输入被控制节点IP，yes，输入节点密码
  ```

>在控制节点，修改/etc/ansible/hosts文件

**常见命令** ：

语法：

```
ansible [-i 主机清单文件] [-f 批次][-m 模块名称][-a 模块参数]
```

```
ansible -i /etc/ansible/hosts test -u root -m command -a 'ls /home' -k
其中 -i , -m 可以省略，-k是输入密码
-i inventory
-m moudle
-u root
命令可以简写为
ansible  webservers -a 'ls /shell'
```

> Inventory格式

> 连续主机书写格式、方括号里面写上主机IP，可以省略

192.168.0.[81:82:83]

ansible all -m ping

all所有主机

ansible_ssh_user用于指定用于管理远程主机的账号

```
[mfs:children]
mfs_master
mfs_logger
[mfs_master]
192.168.1.190
[mfs_logger]
192.168.1.191
用于指定模块，和定义子模块
```

常用模块

>ansible-doc -s raw 查看ansible的帮助文档

ansible-doc -l 查看模块

- setup 客户端的一些信息

  - filter:过滤主机信息

    ```
    ansible mfs_logger -m setup -a "filter=ansible_selinux"
    ```

- ping 检测主机是否存活

- file ,使用ansible可以用来设置文件属性，熟悉脚本的人可以直接在command或者shell模块设定，不过用file模块，可以增加读起来的一致性。

  参数 -a

   -  state=link

      ```
      ansible mfs_master -m file -a "src=/etc/fstab dest=/tmp/fstab state=link "
      创建/etc/fstab的软链接
      ```

   -  state=absent

      ```
      ansible mfs_master -m file -a "path=/tmp/fstab state=absent"
      删除/tmp/fstab下的文件
      ```

  - state=touch

    ```
    ansible mfs_master -m file -a "path=/tmp/file1 state=touch"
    创建一个文件
    ```

  - state=directory

    ```
    ansible mfs_master -m file -a "path=/tmp/d1 state=directory owner=root group=root mode=777"
    创建一个属主，属组都为root，权限是777的一个目录
    ```


- copy

  - ```
    ansible mfs -m copy -a "src=justtest dest=/tmp/file2 mode=755 owner=root group=root"
    将源主机的justtest拷贝到目标主机的/tmp,并重命名为file2
    其中mode,owner,group可以不写，‘=’号中间不要有空格，双引号单引号都是可以的！
    ```

  - backup 目标主机有该文件是否做备份

    ```
    ansible mfs -m copy -a 'src=justtest dest=/tmp/file4 backup=yes'
    ```

- command

  - chdir 改变工作路径

    ```
    ansible all -a 'chdir=/usr/local tar czf bin.tar.gz bin'      //进入/usr/local目录，压缩
    ```

- shell(支持管道、与raw类似)

  >Tips：使用module command或者shell或者raw都能调用对象机器上的某条指令或者某个可执行文件。

- service 

  - name

  - state=started、stopped、restarted

  - sleep=3 #间隔时间

  - runlevel

  - enabled=yes、no

  - arguments

  - network args=eth0

    ```
    ansible mfs_master -m service -a 'name=httpd state=started enabled=yes'
    #开启httpd服务，并且设置开机自启动
    ansible mfs_master -m shell -a 'service httpd start' 
    #也可以这样写
    ```

- cron

- filesystem

- yum

  - config_file :yum配置文件

  - name:指定软件包的名字

  - state:状态

    ```
    ansible mfs_logger -m yum -a 'name=lrzsz'        #yum安装软件
    ansible mfs_logger -m yum -a 'name=lrzsz state=absent' #yum删除软件
    ```

- user

  ```
  ansible mfs_master -m user -a "name=test01 group=root"                  #添加用户
  ansible mfs_master -m user -a "name=test01 state=absent remove=yes"     #删除用户
  ```

- group

  ```
  ansible host32 -m group -a "name=testgrp01"
  ansible host32 -m group -a "name=testgrp01 state=absent"
  ```

  ​

  ​









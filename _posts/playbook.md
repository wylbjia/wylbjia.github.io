# Playbook

Playbook，顾名思义就是剧本的意思。

**首先** 写一个简单的Playbook，让我们先认识一下这个东东。

```
---
- hosts: mfs_master
  user: root
  tasks:
    - name: playbook_test
      shell: touch /tmp/playbook.txt
```

>- hosts参数指定在那个主机进行操作
>- user参数指定使用什么用户登录远程主机操作，默认使用root,一般不加这个**选项**
>- tasks指定一个任务，name是任务的名称
>- shell指定一个模块，和Ad-hoc里面的类似

Playbook的写作遵循YAML格式，关于这个格式，我也不是很了解...主要要注意以下几点吧，

- 开头以---表示这是一个YAML格式的文件，有的说结尾要以...但是不加好像也没事。
- -和：后面必须要有空格
- 要注意代码的缩进，采用相同的缩进表示同一层级的数据

执行命令ansible-playbook + 文件名

```
[root@localhost playbook]# ansible-playbook test.yml 

PLAY [mfs_master] ************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************
ok: [192.168.1.190]

TASK [playbook_test] *********************************************************************************************
changed: [192.168.1.190]

PLAY RECAP *******************************************************************************************************
192.168.1.190              : ok=2    changed=1    unreachable=0    failed=0   
```

​	我们可以看到有个名为Gathering Facts的Task执行了，可是我们并没有在任务里写这个，这是为啥呢？

这个就是所谓的setup模块，我们可以设置参数gather_facts：false，这样这个任务就不会执行了。

​	下面我们来介绍一下在Playbook里面使用变量，还有一些常用的判断语句，来增加文件的可读性，写出更加优秀的文档吧！  	

- 变量

- 环境变量：暂时不学

  > ansible变量引用使用{{}}，在vars后设定变量

  下面我们看个例子：

  ```
  ---
    - hosts: mfs_logger
      vars:
        - message: hello-world
      gather_facts: false
      tasks:
        - name: say hello task
          shell: echo {{message}} `date` by `hostname` > /tmp/hello.log
  ```

  到目标主机上查看，发现已经实现了变量的替换。

- 用命令行传递参数

- 定义命令行变量

  ```
  ---
  - hosts: '{{ hosts }}'
    user: '{{ user }}'
    tasks:
      - name: playbook_test
        shell: touch /tmp/playbook.txt
  ```

  - 在命令行传值

    ```
    controll]# ansible-playbook test.yml --extra-vars "hosts=compute2,user=root"
    ```

  - 用json格式传递参数

    ```
    ansible-playbook test.yml --extra-vars "{'hosts':'compute2', 'user':'root'}" 
    ```

  - 还可以将参数放在文件里

    ```
    ansible-playbook test.yml --extra-vars "@vars.json"
    ```

    ​

- 条件判断

  when，语句

  ```
  ---
    - hosts: mfs_logger
      gather_facts: true
      tasks:
        - name: say redhat hello task
          shell: echo "Redhat" `date` by `hostname` >> /tmp/hello.log
          when: ansible_os_family == "RedHat"
        - name: say other linux hello task
          shell: echo "Redhat" `date` by `hostname` >> /tmp/hello.log
          when: ansible_os_family != "RedHat"
  ```

  我们可以看到命令的执行效果。

  ```
  [root@localhost playbook]# ansible-playbook hello.playbook 

  PLAY [mfs_logger] ************************************************************************************************

  TASK [Gathering Facts] *******************************************************************************************
  ok: [192.168.1.191]

  TASK [say redhat hello task] *************************************************************************************
  changed: [192.168.1.191]

  TASK [say other linux hello task] ********************************************************************************
  skipping: [192.168.1.191]

  PLAY RECAP *******************************************************************************************************
  192.168.1.191              : ok=2    changed=1    unreachable=0    failed=0   
  ```



- 循环

  ```
  - hosts: host31
    gather_facts: true
    tasks:
      - name:  say redhat hello task
        shell: echo {{item}}  `date` by `hostname` >> /tmp/hello.log
        with_items:
          - message item1
          - message item2
          - message item3
          - message item4
          - message item5
  ```

  ​


  ​																	edit wylbjia 18/3/12

  ​		
# SDN

## Abstract

项目计划部署在阿里云上，有关Software Defined Network，所有子模块均来自[Netgroup Research Group](https://github.com/netgroup)，项目目前仍在部署中，这里主要负责记录项目部署过程中的注意事项。(实时更新中)

## SubModules

- [Dreamer-Topology3D](https://github.com/netgroup/Dreamer-Topology3D.git)
- [Dreamer-Experiment-Handler](https://github.com/netgroup/Dreamer-Experiment-Handler.git)
- [Dreamer-Topology-and-Service-Validator](https://github.com/netgroup/Dreamer-Topology-and-Service-Validator.git)
- [OSHI-SR-dataplane-extensions](https://github.com/netgroup/OSHI-SR-dataplane-extensions.git)
- [SDN-TE-SR-tools](https://github.com/netgroup/SDN-TE-SR-tools.git)
- [Dreamer-Mininet-Extensions](https://github.com/netgroup/Dreamer-Mininet-Extensions.git)
- [Dreamer-Topology-Parser-and-Validator](https://github.com/netgroup/Dreamer-Topology-Parser-and-Validator.git)
- [Dreamer-VLL-Pusher](https://github.com/netgroup/Dreamer-VLL-Pusher.git)
- [OpenvSwitch](https://github.com/openvswitch/ovs.git)
- [Mininet](https://github.com/mininet/mininet.git)

## 环境配置

- Ubuntu 16.04
- Apache2.0
- gcc
- python2.7

## 部署流程

### Dreamer-Topology-and-Service-Validator

1. python依赖

    ```sh
    pip install django==1.6
    pip install networkx
    ```
2. 下载并启动项目

    ```sh
    git clone https://github.com/netgroup/Dreamer-Topology-and-Service-Validator.git
    cd Dreamer-Topology-and-Service-Validator
    python manage.py runserver 0.0.0.0:8090
    ```

### Dreamer-Topolpgy3D

1. `git clone https://github.com/netgroup/Dreamer-Topology3D.git`
2. 将Dreamer-Topology挂在到服务器跟目录下，我用的是apache2，如下：
    
    `mv Dreamer-Topology3D ~/webroot/`
    
3. 修改项目中的**js/src/config.js**文件如下：
 
    ```js
    this.top_ser_validator = {};
    this.top_ser_validator.host_port = 8090;
    this.top_ser_validator.host_name "{ip_address}";

    //Experiment Handler
    this.experiment_handler = {};
    this.experiment_handler.host_port = 3000;
    this.experiment_handler.host_name = "{ip_address}";
    ```
    这里服务器地址我填的是公网ip，填写127.0.0.1的时候会报错，修改过apache配置文件后依旧无果，毕竟不是啥大问题，等以后再解决把。
    
4. 打开浏览器输入url即可访问


## Dreamer-Mininet-Extensions

1. Clone仓库
    ```sh
    git clone https://github.com/netgroup/Dreamer-Mininet-Extensions.git
    git clone https://github.com/netgroup/Dreamer-VLL-Pusher.git
    git clone https://github.com/openvswitch/ovs.git  
    git clone https://github.com/mininet/mininet.git
    git clone https://github.com/netgroup/Dreamer-Topology-Parser-and-Validator.git
    ```

2. python依赖

    ```sh
    pip install netaddr
    pip install ipaddress
    pip install ryu
    pip install networkx
    ```
    
3. 系统依赖

    ```sh 
    apt install autoconf 
    apt install automake 
    apt install libtool
    apt install quagga
    ```
    
3. Open vSwitch Service安装

    ```sh
    cd ovs
    git checkout v2.3   # 切换成2.3版本
    
    ./boot.sh   # 可能需要root权限
    ./configure
    make
    make check      # 检查单元测试
    make install 
    export PATH=$PATH:/usr/local/share/openvswitch/scripts
    ovs-ctl start
    
    cd Dreamer-Mininet-Extensions
    chmod +x install_ovswitch.sh
    ./install_ovswitchd.sh
    ```
    
4. Mininet安装
    
    ```sh
    cd mininet
    git reset --hard aae0affae46a63ef5e54d86351c96417c3888112
    cd util
    chmod +x install.sh
    ./install.sh -ent
    ``` 
    
5. 修改`Dreamer-Mininet-Extensions`目录中的文件路径  
    - **mininet_deployer.py**中设置`parser_path={workspace}/Dreamer-Topology-Parser-and-Validator`
    - **mininet_extensions.py**中设置`RYU_PATH={workspace}/Dreamer-VLL-Pusher/ryu`
    - **mininet_extensions.py**中设置`PROJECT_PATH={workspace}/Dreamer-Mininet-Extensions`

6. 运行测试
     
     ```sh
     ./mininet_deployer.py --topology topo/topo_vll_pw.json
     ```
     
具体安装方法详见：[Dreamer-Mininet-Extensions How-To](http://netgroup.uniroma2.it/twiki/bin/view/Oshi/OshiExperimentsHowto#MininetExtensions)
    
## Dreamer-Experiment-Handler

1. `git clone https://github.com/netgroup/Dreamer-Experiment-Handler.git`
2. 修改配置文件*config.js*

    ```js
    config.mininet.mininet_extension_path = "{workspace}/Dreamer-Mininet-Extensions";
    ```
    
3. 运行

    ```sh
    cd Dreamer-Experiment-Handler
    node app.js
    ```
    
## 未完待续

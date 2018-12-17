# SDN

## Abstract

项目计划部署在阿里云上，有关Software Defined Network，所有子模块均来自[Netgroup Research Group](https://github.com/netgroup)，项目目前仍在部署中，这里主要负责记录项目部署过程中的注意事项。(实时更新中)

## SubModules

- [Dreamer-Topology3D](https://github.com/jiahao-shen/Dreamer-Topology3D.git)
	- 一个简单直观的JavaScript Web GUI（虽然他们官方的[Demo](http://stud.netgroup.uniroma2.it/OSHI/TopoDesigner/)都早早的挂了），可以通过扩展支持不同的网络模型，目前支持标准OpenFlow设备网络和由OSHI节点构成的网络，可以导出或导入JSON格式的拓扑。还支持图形编辑拓扑。
	
- [Dreamer-Experiment-Handler](https://github.com/jiahao-shen/Dreamer-Experiment-Handler.git)
    - 控制emulated SDN testbeds与Topology3D提供的Web GUI进行交互，使用Mininet仿真器，提供服务端元素来分配和控制Mininet节点，将Mininet控制台中的信息重定向到Web GUI上进行显示。

- [Dreamer-Topology-and-Service-Validator](https://github.com/jiahao-shen/Dreamer-Topology-and-Service-Validator.git)
    - 一个Django应用程序，用于存储model/layer及其约束，并能够检验基于Dreamer-Topology3D项目创建的拓扑。
    
- [OSHI-SR-dataplane-extensions](https://github.com/jiahao-shen/OSHI-SR-dataplane-extensions.git)
    - OSHI的分段路由数据平面扩展

- [SDN-TE-SR-tools](https://github.com/jiahao-shen/SDN-TE-SR-tools.git)
    - 基于SDN流量工程和分段路由的工具
    - parsers-generators
    - java-te-sr
    - OSHI-SR-pusher

- [Dreamer-Mininet-Extensions](https://github.com/jiahao-shen/Dreamer-Mininet-Extensions.git)
    - 用于OSHI实验的Mininet扩展，用来模拟使用Mininet仿真器的OSHI网络
    
- [Dreamer-Topology-Parser-and-Validator](https://github.com/jiahao-shen/Dreamer-Topology-Parser-and-Validator.git)
    - 用于Mininet和测试部署的拓扑解析器。使用此工具，您可以解析和验证由Dreamer-Topology-Designer创建的拓扑。
    
- [Dreamer-VLL-Pusher](https://github.com/jiahao-shen/Dreamer-VLL-Pusher.git)
- [dreamer-ryu](https://github.com/jiahao-shen/dreamer-ryu.git)
- [Mantoo-scripts-and-readme](https://github.com/jiahao-shen/Mantoo-scripts-and-readme.git)

## 环境配置

- Ubuntu 16.04
- Apache2
- gcc
- python2.7
- nodejs

## 部署流程

### Dreamer-Topology-and-Service-Validator

- python依赖

    ```sh
    pip install django==1.6
    pip install networkx==1.9
    ```
- 下载并启动项目

    ```sh
    git clone https://github.com/netgroup/Dreamer-Topology-and-Service-Validator.git
    cd Dreamer-Topology-and-Service-Validator
    python manage.py runserver 0.0.0.0:8090
	

    # 后台运行
	nohup python manage.py runserver 0.0.0.0:8090 &
    ```

### Dreamer-Topolpgy3D

- `git clone https://github.com/netgroup/Dreamer-Topology3D.git`
- 将Dreamer-Topology挂在到服务器跟目录下，我用的是apache2，如下：
    
    `mv Dreamer-Topology3D {webroot}`
    
- 修改项目中的**js/src/config.js**文件如下：
 
    ```js
    this.top_ser_validator = {};
    this.top_ser_validator.host_port = 8090;
    this.top_ser_validator.host_name "{ip_address}";

    //Experiment Handler
    this.experiment_handler = {};
    this.experiment_handler.host_port = 3000;
    this.experiment_handler.host_name = "{ip_address}";
    ```
    
- 打开浏览器输入url即可访问
- 项目会默认从[googleapis](https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,600,700,300italic,400italic,600italic)处加载字体，导致前端加载比较慢，可以先从源地址下载**fonts.css**文件到**Dreamer-Topology3D/dist/css**目录下，然后将**AdminLTE.css**的**第1行**改为`@import url(fonts.css)`
- **Dreamer-Topology3D/index.html**中**第1087行**的js文件名写错了，改成**plugins/datatables/dataTables.bootstrap.min.js**


## Dreamer-Mininet-Extensions

- Clone仓库
    ```sh
    git clone https://github.com/netgroup/Dreamer-Mininet-Extensions.git
    git clone https://github.com/netgroup/Dreamer-VLL-Pusher.git 
    git clone https://github.com/netgroup/Dreamer-Topology-Parser-and-Validator.git
    ```

- python依赖

    ```sh
    pip install netaddr
    pip install ipaddress
    pip install ryu
    pip install siphash
    ```
    
- 系统依赖

    ```sh 
    apt install autoconf 
    apt install automake 
    apt install libtool
    apt install quagga
    ```
    
- Mininet安装

    ```sh
	apt install mininet
	mv /etc/init.d/openvswitch-switch /etc/init.d/openvswitchd
    ```

	    
- 修改**Dreamer-Mininet-Extensions**目录中的文件路径  
    - **mininet_deployer.py**中设置`parser_path={workspace}/Dreamer-Topology-Parserd`
    - **mininet_extensions.py**中设置`RYU_PATH={workspace}/dreamer-ryu/ryu/app`
    - **mininet_extensions.py**中设置`PROJECT_PATH={workspace}/Dreamer-Mininet-Extensions`

- 运行测试
     
     ```sh
     ./mininet_deployer.py --topology topo/topo_vll_pw.json 
     ```
     
- 具体安装方法详见：[Dreamer-Mininet-Extensions How-To](http://netgroup.uniroma2.it/twiki/bin/view/Oshi/OshiExperimentsHowto#MininetExtensions)
    
## Dreamer-Experiment-Handler

- `git clone https://github.com/netgroup/Dreamer-Experiment-Handler.git`
- 修改配置文件**config.js**

    ```js
    config.mininet.mininet_extension_path = "{workspace}/Dreamer-Mininet-Extensions";
    ```
    
- 运行

    ```sh
    cd Dreamer-Experiment-Handler
    node app.js

	# 后台运行
	nohup node app.js & 
    ```
    
## OSHI-SR-dataplane-extensions

```sh
git clone https://github.com/netgroup/OSHI-SR-dataplane-extensions.git
cd OSHI-SR-dataplane-extensions
make
mv fpm-of.bin /usr/bin/
```

## SDN-TE-SR-tools

- 依赖和Clone

    ```sh
    apt install python-tk
    pip install matplotlib==2.2

    git clone https://github.com/netgroup/SDN-TE-SR-tools.git)
    git clone https://github.com/netgroup/dreamer-ryu.git
    git clone https://github.com/netgroup/Mantoo-scripts-and-readme.git
    ```

- 修改**Dreamer-Mininet-Extensions/nodes.py**文件中的**第32行**:

    ```py
    ENABLE_SEGMENT_ROUTING = True
    ```
    
## Small scale Topology

- 在浏览器打开Topology3D GUI加载样例拓扑，点击右上角菜单栏的"Topology"，选择"Import topology from file"，选择**SDN-TE-SR-tools/parsers-generators/t3d/small-topo2-4-vll.t3d**文件
- 选择左侧菜单栏的"Deployment"，点击"Deploy"，在下方命令行输入`deploy`并回车
- 查看Controller的IP并远程登录

    ```sh
    ssh -X root@10.255.245.1
    cd Mantoo-scripts-and-readme
    ```
- 修改**ryu_start.sh**文件中的目录为**dreamer-ryu/ryu/app**
- 运行`./ryu_start.sh`
- 或者手动运行
		
    ```sh
    ssh -X root@10.255.245.1
    cd {workspace}/dreamer-ryu/ryu/app
    ryu-manager rest_topology.py ofctl_rest.py --observe-links
    ```
		
- 修改**generate_topo_and_flow_cata.sh**文件中的目录为**SDN-TE-SR-tools/parsers-generators**
- 生成SR分配算法所需的流目录

    ```sh
    cd Mantoo-scripts-and-readme
    ./generate_topo_and_flow_cata.sh 10.255.245.1:8080
    ```
- 或者手动运行

    ```sh
    cd {workspace}/SDN-TE-SR-tools/parsers-generators
    python parse_transform_generate.py --in ctrl_ryu --out nx --generate_flow_cata_from_vll_pusher_cfg --controller 10.255.245.1:8080
    mv flow_catalogue.json ../java-te-sr/flow/
    mv links.json ../java-te-sr/topology/
    mv nodes.json ../java-te-sr/topology/
    ```
    
- 查看生成的文件

    ```sh
    cat {workspace}/SDN-TE-SR-tools/java-te-sr/flow/flow_catalogue.json
    ```
- 运行SR分配算法
    - 打开Intellij IDEA，导入**SDN-TE-SR-tools/java-te-sr**项目，选择从Eclipse项目导入
    - 使用JDK1.8运行（原项目使用JDK1.7）
    - 设置运行参数
    
    ```sh
    topo_in=topology/links.json
    topo_out=topology/out_links.json
    flows_in=flow/flow_catalogue.json
    flows_out=flow/out_flow_catalogue.json
    ```
    - 在IDEA中选择"Tools"->"Deployment"->"Configuration"，选择传输协议，配置服务器ip地址以及跟目录，在"Mappings"中配置本地项目路径以及对应的服务端项目路径，配置成功后选择"Tools"->"Deployment"->"Browser Remote Host"即可在右侧看到服务器端文件，以方便在本地和服务器双方进行文件的同步。
    - 点击运行，如果成功则控制台不会显示任何信息

- 把本地生成的**out_flow_catalogue.json**文件同步到服务器后，将其放到**SDN-TE-SR-tools/OSHI-SR-pusher**路径下

    ```sh
    cd {workspace}/Mantoo-scripts-and-readme
    ./sr_pusher_start.sh 10.255.245.1:8080 --add
    ```
    
- 或者手动运行

    ```sh
    cd {workspace}/SDN-TE-SR-tools
    mv java-te-sr/flow/out_flow_catalogue.json OSHI-SR-pusher/
    cd {workspace}/SDN-TE-SR-tools/OSHI-SR-pusher/
    rm sr_vlls.json
    ./sr_vll_pusher.py --controller 10.255.245.1:8080 --add
    ```
- 未完待续

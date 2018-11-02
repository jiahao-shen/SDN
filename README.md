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
- [dreamer-ryu](https://github.com/netgroup/dreamer-ryu.git)
- [Mantoo-scripts-and-readme](https://github.com/netgroup/Mantoo-scripts-and-readme.git)

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

    ```

	修改**Dreamer-Mininet-Extensions/nodes.py**文件中的第**104**行:

	```python
	ovs_initd = "/etc/init.d/openvswitch-switch"
	```
    
- 修改**Dreamer-Mininet-Extensions**目录中的文件路径  
    - **mininet_deployer.py**中设置`parser_path={workspace}/Dreamer-Topology-Parser-and-Validator`
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
    cd Mantoo-scripts-and-readm
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
	
    - 点击运行，如果成功则控制台不会显示任何信息

- 未完待续


# container-centric fiware demonstaration

This repository construct a container-centric [FIWARE](http://www.fiware.org/) demonstration on [Kubernetes](https://kubernetes.io/).

![conrainer-centric-fiware-demonstration.png](/docs/images/container-centric-fiware-demonstration.png)

|component|summary|
|:--|:--|
|[kubernetes](https://kubernetes.io/)|Container Orchestration Platform|
|[ambassador](https://www.getambassador.io/)|API Gateway|
|fiware-ambssador-auth|Authorization and Authentication component working with ambassador|
|fiware-cmd-proxy|Business Logic component working with FIWARE orion|
|[FIWARE orion](https://catalogue-server.fiware.org/enablers/publishsubscribe-context-broker-orion-context-broker)|Publish/Subscribe Context Broker|
|[FIWARE cygnus](https://catalogue-server.fiware.org/enablers/cygnus)|Data collection and Persistence Agent|
|[FIWARE iotagent-ul](https://catalogue-server.fiware.org/enablers/backend-device-management-idas)|Backend Device Management Agent|
|fiware-mqtt-msgfilter|MQTT message duplication checker working with FIWARE iotagent-ul and etcd|
|[etcd](https://coreos.com/etcd/docs/latest/)|Distributed Key-Value Store in order to manege distributed lock|
|[VerneMQ](https://vernemq.com/)|Distributed MQTT Broker|
|[MongoDB](https://www.mongodb.com/)|Document-oriented NoSQL Database|

|device|summary|
|:--|:--|
|fiware-gamepad-controller|Gamepad Controller|
|fiware-ros-turtlesim|ROS package to act as a bridge FIWARE orion and ROS demo node (turtlesim)|
|fiware-ros-gopigo|ROS package to act as a bridge FIWARE orion and gopigo|

## Requirements

* kubernetes client PC

||version|
|:--|:--|
|OS|macOS Sierra 10.12.6|
|azure cli|2.0.42|
|kubectl|1.10.2|
|helm|2.9.1|
|envsubst|0.19.8.1|

* minikube host PC

||version|
|:--|:--|
|OS|macOS Sierra 10.12.6|
|VirtualBox|5.2.12 r122591|
|minikube|0.28.1|

* Kubernetes

||version|
|:--|:--|
|Azure AKS|1.10.3|
|minikube|1.10.0|

## getting started

1. install jupyter notebook

    ```bash
    $ cd docs
    $ ./setup_jupyter_notebook.sh
    ```
1. start jupyter notebook

    ```bash
    $ ./start_jupyter_notebook.sh
    ```

### Microsoft Azure AKS

1. setup environment variables

    ```bash
    $ cp azure_aks/env.template azure_aks/env
    $ vi env
    ```
1. prepare Microsoft Azure AKS step by step using [/docs/azure_aks/1_prepare_aks.ipynb](/docs/azure_aks/1_prepare_aks.ipynb).
1. start pods on Kubernetes step by step using [/docs/azure_aks/2_start_pods.ipynb](/docs/azure_aks/2_start_pods.ipynb).
1. register iot device & robot to fiware step by step using [/docs/azure_aks/3_register_device.ipynb](/docs/azure_aks/3_register_device.ipynb).
1. register business logic to fiware step by step using [/docs/azure_aks/4_register_business_logic.ipynb](/docs/azure_aks/4_register_business_logic.ipynb).
1. operate turtlebot3 step by step using [/docs/azure_aks/5_operate_turtlebot3.ipynb](/docs/azure_aks/5_operate_turtlebot3.ipynb).
1. visualize the data of turtlebot3 step by step using [/docs/azure_aks/6_visualize_data.ipynb](/docs/azure_aks/6_visualize_data.ipynb).

### minikube

1. setup environment variables

    ```bash
    $ cp minikube/env.template minikube/env
    $ vi env
    ```
1. prepare minikube step by step using [/docs/minikube/1_prepare_minikube.ipynb](/docs/minikube/1_prepare_minikube.ipynb).
1. start pods on Kubernetes step by step using [/docs/minikube/2_start_pods.ipynb](/docs/minikube/2_start_pods.ipynb).
1. register iot device & robot to fiware step by step using [/docs/minikube/3_register_device.ipynb](/docs/minikube/3_register_device.ipynb).
1. register business logic to fiware step by step using [/docs/minikube/4_register_business_logic.ipynb](/docs/minikube/4_register_business_logic.ipynb).
1. operate turtlebot3 step by step using [/docs/minikube/5_operate_turtlebot3.ipynb](/docs/minikube/5_operate_turtlebot3.ipynb).
1. visualize the data of turtlebot3 step by step using [/docs/minikube/6_visualize_data.ipynb](/docs/minikube/6_visualize_data.ipynb).

## Related Repositories
### customized FIWARE components
* [tech-sketch/iotagent-ul](https://github.com/tech-sketch/iotagent-ul)
    * original: [telefonicaid/iotagent-ul](https://github.com/telefonicaid/iotagent-ul)
    * What's the problem?
        * Let's say that you want a iotagent-ul SERVICE which has multiple iotagent-ul PODs on your Kubernetes.
        * When you put a message to iotagent-ul by using HTTP, there is no problem because iotagent-ul SERVICE routes a HTTP message to only one POD.
        * But when you put a message to iotagent-ul by using MQTT, unfortunatly a MQTT message is processed as many times as the number of iotagent-ul PODs. Because the each iotagent-ul PODs subscribes for the same topic of MQTT Broker, so a MQTT message published that topic is proccessed by each PODs individually.
    * How to treat this
        * When a MQTT message is received, the customized iotagent-ul calls a REST API endpoint before processing the MQTT message.
        * If the REST API returns `200 OK`, the customized iotagent-ul continues processing the MQTT message as ordinally.
        * But if the REST API returns `409 Conflict`, the customized iotagent-ul stops processing.
        * To do so, the cluster of iotagent-ul PODs processes only once for a MQTT message.

### FIWARE support components
* [tech-sketch/fiware-ambassador-auth](https://github.com/tech-sketch/fiware-ambassador-auth)
    * A REST API component working with [Ambassador](https://www.getambassador.io/) on Kubernetes in order to authorize and authanticate the client.
    * Bearar Authenticaton and Basic Authentication are supported.
* [tech-sketch/fiware-mqtt-msgfilter](https://github.com/tech-sketch/fiware-mqtt-msgfilter)
    * A REST API component working with [tech-sketch/iotagent-ul](https://github.com/tech-sketch/iotagent-ul) and [etcd](https://coreos.com/etcd/docs/latest/) in order to check the message duplication.

### Business Logic components
* [tech-sketch/fiware-cmd-proxy](https://github.com/tech-sketch/fiware-cmd-proxy)
    * A web application working with [FIWARE orion context broker](https://github.com/telefonicaid/fiware-orion) in order to receive a command from gamepad or web controler and to send a command to ROS robot.
* [tech-ksetch/fiware-robot-visualization](https://github.com/tech-sketch/fiware-robot-visualization)
    * A web application working with [FIWARE cygnus](https://github.com/telefonicaid/fiware-cygnus) in order to visualize the locus of ROS robot.

### gamepad controller
* [tech-sketch/fiware-gamepad-controller](https://github.com/tech-sketch/fiware-gamepad-controller)
    * A python3.6 application in order to receive gamepad events and to send a command corresponding the event to MQTT broker.

### ROS package
* [tech-sketch/fiware_ros_turtlebot3_bridge](https://github.com/tech-sketch/fiware_ros_turtlebot3_bridge)
    * A [ROS](http://wiki.ros.org/) pakage witten by python2 in order to act as a bridge between MQTT broker and ROS nodes.
    * When a MQTT message is received from a MQTT topic, this package create ROS message and publish a ROS message to a ROS topic.
    * At the opposite, when a ROS message is received from a ROS topic, this package publish a MQTT message to a MQTT topic.
* [tech-sketch/fiware_ros_turtlebot3_operator](https://github.com/tech-sketch/fiware_ros_turtlebot3_operator)
    * A [ROS](http://wiki.ros.org/) pakage witten by python2 in order to control "turtlebot3" and receive its odometries.
    * You can use this package with either actual robot or simulator.

## License

[Apache License 2.0](/LICENSE)

## Copyright
Copyright (c) 2018 [TIS Inc.](https://www.tis.co.jp/)

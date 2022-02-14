# Bush Womack TAP App Workshop
Bush Womack TAP App Workshop

## Prerequisites

* Provision a Kubernetes cluster
* [Install TAP](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/index.html) in the cluster
* Install RabbitMQ cluster operator and create RabbitMQ cluster
    * Install [RabbitMQ cluster operator](https://github.com/rabbitmq/cluster-operator)
        * `kapp deploy --app rmq-operator --file https://github.com/rabbitmq/cluster-operator/releases/download/v1.11.1/cluster-operator.yml `

    * Assign permissions
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors-rabbit/master/k8s/rabbitmqcluster-read-write.yaml`

    * Create the "example-rabbitmq-cluster-1" RabbitMQ cluster that will be used by the .NET and Spring Sensors applications (wait for it to complete)
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors-rabbit/master/k8s/rabbitmq-cluster.yaml`

* Register the .NET Sensor application accelerator with TAP
    * `tanzu accelerator create dotnet-sensors-rabbit --git-repository https://github.com/fjb4/dotnet-sensors-rabbit --git-branch master`


## dotnet app
* register the app template in your AppAcc
* open your tap gui
* select spring-sensors-dotnet
* download the zip
* extract
* add to your git org
* tap deploy

## java app
* open your tap gui
* select spring-sensors
* download the zip
* extract
* add to your git org
* tap deploy

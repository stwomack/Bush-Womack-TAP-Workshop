- This works, ```--service-ref "rmq=rabbitmq.com/v1beta1:RabbitmqCluster:default:example-rabbitmq-cluster-1"```
- But this does not:
```
serviceClaims:
    - name: rmq
      ref:
        apiVersion: rabbitmq.com/v1beta1
        kind: RabbitmqCluster
        name: example-rabbitmq-cluster-1
```

- Also, where is app in tap-gui? 

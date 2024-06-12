# Strimzi with CfK

Prerequisites

- running K8s or OpenShift Cluster
- kubectl
- a running Confluent Platform Env and/or Confluent Cloud Cluster (Basic Cluster is enough)
- Internet access


## Strimzi Part

Create a Namespace
```bash
kubectl create namespace confluent
```

set as default namespace
```bash
kubectl config set-context --current --namespace confluent
```

Deploy the needed ressources for the Strimzi operator


```bash
kubectl apply -f 1_sa_strimzi.yml -n confluent

kubectl apply -f 2_strimzi_role.yml -n confluent

kubectl apply -f 2_strimzi_role.yml 

kubectl apply -f 3_rb_strimzi.yml -n confluent

kubectl apply -f 4_crd_kafkatopic.yml -n confluent

```

## Confluent Platform

Set up the Helm Chart
```bash
helm repo add confluentinc https://packages.confluent.io/helm
```


Install Confluent For Kubernetes using Helm
```bash
helm upgrade --install confluent-operator confluentinc/confluent-for-kubernetes --namespace confluent
```
Deploy Confluent Platform

```bash
kubectl apply -f confluent-platform.yml -n confluent
```

Check the pods
```bash
kubectl get pods -n confluent
```
start the strimzip topic operator
```bash
kubectl apply -f strimzi-topic-op.yml
```

check if the pod and deployment comes up without any errors

```bash
kubectl get pods  | grep strimzi
strimzi-topic-operator-556978bd5c-qc4qv                  1/1     Running   0          118s

kubectl get deployments
NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
strimzi-topic-operator                1/1     1            1           2m19s
```

Create a topic
```bash
kubectl apply -f demo-topic.yml -n confluent
```
Topic 

## Confluent Cloud

Create a basic Confluent Cloud
adapt bootstrap address in strimzi-topic-op-cc (line 35 & 61)

```yaml
            - name: STRIMZI_KAFKA_BOOTSTRAP_SERVERS
              value: pkc-75m1o.europe-west3.gcp.confluent.cloud:9092
              "sasl.bootstrap.servers": "pkc-75m1o.europe-west3.gcp.confluent.cloud:9092"   
```

Create an API Key and Secret 
Adapt the username and password in line 62 of strimzi-topic-op-cc accordingly.

```yaml
                  "sasl.jaas.config": "org.apache.kafka.common.security.plain.PlainLoginModule required username='AC3HUQAWH2ZGBMO2' password='sH6ebHOZ8xNgQbdIOr0LHkkz99S6bjYKdNbwBsbZHzb9FKfoulo2fO/9f8wI1yaY';"
```                  

create the strimzi topic operator for confluent cloud
```bash
kubectl apply -f strimzi-topic-op-cc.yml
```

a second pod should come (called strimzi-topic-operator-cc-...)

wait until the pod is running.

create a new topic in CC




### Hi there 👋

<!--
**amir-naik/amir-naik** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on Open Source Apache Products such as Druid and Superset
- 🌱 I’m currently learning Apache Airflow
- 👯 I’m looking to collaborate on ... Open Source Apache Products
- 🤔 I’m looking for help with ... Production Ready Installation and Cloud Ready Optimizations 
- 💬 Ask me about ...Apache Druid and Superset
- 📫 How to reach me: ...amir.naik@teksecur.com
- 😄 Pronouns: ...Amir
- ⚡ Fun fact: ...
-->

Install Druid Operator:-
=======================
PS C:\Users\kumar> git clone https://github.com/druid-io/druid-operator.git
Cloning into 'druid-operator'...
remote: Enumerating objects: 13609, done.
remote: Counting objects: 100% (415/415), done.
remote: Compressing objects: 100% (243/243), done.
remote: Total 13609 (delta 220), reused 266 (delta 119), pack-reused 13194
Receiving objects: 100% (13609/13609), 59.03 MiB | 1.64 MiB/s, done.
Resolving deltas: 100% (5450/5450), done.
PS C:\Users\kumar>

PS C:\Users\kumar\druid-operator\chart> helm upgrade --install druid-operator . -n druid-operator --create-namespace --set env.DENY_LIST="kube-system"
NAME: druid-operator
LAST DEPLOYED: Sun Mar 27 22:45:06 2022
NAMESPACE: druid-operator
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Refer to https://github.com/druid-io/druid-operator/blob/master/docs/README.md to get started.
PS C:\Users\kumar\druid-operator\chart>
PS C:\Users\kumar\druid-operator\chart>

Delete Existing PODS
===================
kubectl delete pod druid-tiny-cluster-brokers-0 --now
kubectl delete pod druid-tiny-cluster-coordinators-0 --now
kubectl delete pod druid-tiny-cluster-historicals-0 --now
kubectl delete pod druid-tiny-cluster-routers-0 --now
kubectl delete pod tiny-cluster-zk-0 --now

Delete Existing Services
===================
kubectl delete services druid-tiny-cluster-brokers
kubectl delete services druid-tiny-cluster-coordinators
kubectl delete services druid-tiny-cluster-historicals
kubectl delete services druid-tiny-cluster-routers

PS C:\Users\kumar> kubectl get deployments --all-namespaces
>>
NAMESPACE        NAME              READY   UP-TO-DATE   AVAILABLE   AGE
default          superset          1/1     1            1           8d
default          superset-worker   1/1     1            1           8d
druid-operator   druid-operator    1/1     1            1           9d
kube-system      coredns           2/2     2            2           9d

Delete Deployment
===================
kubectl delete -n druid-operator deployment druid-operator

After Delete :-
PS C:\Users\kumar> kubectl get deployments --all-namespaces
>>
NAMESPACE     NAME              READY   UP-TO-DATE   AVAILABLE   AGE
default       superset          1/1     1            1           8d
default       superset-worker   1/1     1            1           8d
kube-system   coredns           2/2     2            2           9d

kubectl describe pod druid-tiny-cluster-brokers-0 
	Controlled By:  StatefulSet/druid-tiny-cluster-brokers
kubectl delete statefulsets druid-tiny-cluster-brokers

kubectl describe pod druid-tiny-cluster-coordinators-0 
Controlled By:  StatefulSet/druid-tiny-cluster-coordinators
kubectl delete statefulsets druid-tiny-cluster-coordinators

kubectl describe pod druid-tiny-cluster-historicals-0
kubectl delete statefulsets druid-tiny-cluster-historicals

kubectl describe pod druid-tiny-cluster-routers-0
kubectl delete statefulsets druid-tiny-cluster-routers

kubectl describe pod tiny-cluster-zk-0
kubectl delete statefulsets tiny-cluster-zk


Deploy the Tiny Cluster
===========================
Install ZooKeepr:-

kubectl apply --validate -f https://raw.githubusercontent.com/druid-io/druid-operator/master/examples/tiny-cluster-zk.yaml

PS C:\Users\kumar> kubectl apply --validate -f tiny-cluster.yaml
druid.druid.apache.org/tiny-cluster created

Check The Druid Processes
============================
PS C:\Users\kumar\druid-operator\chart> kubectl get pods
NAME                READY   STATUS              RESTARTS   AGE
tiny-cluster-zk-0   0/1     ContainerCreating   0          10s

PS C:\Users\kumar> kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
druid-tiny-cluster-brokers-0        1/1     Running   0          2m8s
druid-tiny-cluster-coordinators-0   1/1     Running   0          2m8s
druid-tiny-cluster-historicals-0    1/1     Running   0          2m8s
druid-tiny-cluster-routers-0        1/1     Running   0          2m8s
tiny-cluster-zk-0                   1/1     Running   0          22h

kubectl describe pod druid-tiny-cluster-routers-0

kubectl expose deployment example --port=8765 --target-port=9376 \
        --name=example-service --type=LoadBalancer

Start the Druid UI by Port Forwarding :-
==========================================
PS C:\Users\kumar> kubectl port-forward service/druid-tiny-cluster-routers 8888:8088
Forwarding from 127.0.0.1:8888 -> 8088
Forwarding from [::1]:8888 -> 8088

Delete a Service
===============
kubectl delete services router-druid-tiny-cluster-routers-service

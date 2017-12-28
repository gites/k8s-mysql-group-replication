# k8s-mysql-group-replication
PoC for setting up MySQL Group Replication in Master-Master mode on Kubernetes

This work is based on K8s Statefull Sets documentation [https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/]

# Requirements
- PersistentVolume provisioner with a default StorageClass (or statically provisioned PersistentVolumes)
- Kubernetes Cluster v 1.9 (StatefulSets are stable (GA) in 1.9)

# Deployment and scaling
```
# kubectl apply -f *.yaml

# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
mysql-0   2/2       Running   0          10s
```
```
# kubectl scale statefulset --replicas=3 mysql
# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
mysql-0   2/2       Running   0          10m
mysql-1   2/2       Running   0          5md
mysql-2   0/2       Pending   0          14s
```
```
# kubectl -n mysql-test exec -ti mysql-0 mysql -- mysql -e'select * from performance_schema.replication_group_members;'
Defaulting container name to mysql.
Use 'kubectl describe pod/mysql-0 -n mysql-test' to see all of the containers in this pod.
+---------------------------+--------------------------------------+---------------+-------------+--------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST   | MEMBER_PORT | MEMBER_STATE |
+---------------------------+--------------------------------------+---------------+-------------+--------------+
| group_replication_applier | 14d265ce-e714-11e7-83eb-2a28a3915e90 | mysql-0.mysql |        3306 | ONLINE       |
| group_replication_applier | 331149cf-e714-11e7-999d-ea83a38cf6a7 | mysql-1.mysql |        3306 | ONLINE       |
| group_replication_applier | c9d9f4a8-ebd1-11e7-afe2-0e373b025b47 | mysql-2.mysql |        3306 | ONLINE       |
+---------------------------+--------------------------------------+---------------+-------------+--------------+
```
# Future work
- scripts cleanup
- integrate with ProxySQL



### create Storage class
  Local file system  <br>
  Allow expansion set to true
```
$ kubectl create -f mysc.yaml 
```


### create Persistent Volume 
  storage size : 1Gi  <br>
  accessModes: ReadWriteOnce  <br>
  persistentVolumeReclaimPolicy: Recycle  <br>
```
$ kubectl create -f mypv.yaml 
```


### create Persistent Volume Claim 
 accessModes: ReadWriteOnce  <br>
 storage request size : 100Mi  <br>
```
$ kubectl create -f mypvc.yaml 
```
#### check volume status 
verify that status showing bound  <br>
```
$ kubectl get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS    REASON   AGE
my-pv   1Gi        RWO            Recycle          Bound    default/my-pvc   local-storage            19m

$ kubectl get pvc
NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS    AGE
my-pvc   Bound    my-pv    1Gi        RWO            local-storage   24m

```


### create pod 
kubectl create -f mypod.yaml 

### on the node machine 
cat /var/output/out.txt


to be updated

# create Storage class
kubectl create -f mysc.yaml 

# create Persistent Volume 
kubectl create -f mypv.yaml 

# create Persistent Volume Claim 
kubectl create -f mypvc.yaml 

# create pod 
kubectl create -f mypod.yaml 

kubectl get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS    REASON   AGE
my-pv   1Gi        RWO            Recycle          Bound    default/my-pvc   local-storage            19m

kubectl get pvc
NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS    AGE
my-pvc   Bound    my-pv    1Gi        RWO            local-storage   24m

# Deploy any Stateful application on Kubernetes as StatefulSet. If you have no ideas, you can use minio.

According official Minio documentation, to run Minio we can use docker 

    docker run -p 9000:9000 \
        -e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
        -e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
    minio/minio server /data

So, we just need to create StatefullSet from this.   

At first, it will be good if we have secret to store variables in it. Let's create this:

    k create ns minio
    k create secret generic --from-literal=MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE --from-literal=MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY minio-secret -n minio

At second, let's create PV for our StatefuleSet:   

    k apply -f pv_host_path5.yaml

Since I already have 4 Persistent Volumes for the previous Stateful set, I have to create 5th for the new one. For every Replica we should create another PV

Time to create s statefull application, using minio_stateful_set.yaml file:

    k apply -f minio_stateful_set.yaml

Let's look at it. First let's check PV and Newly created PVC in minio namespace:

    k get pv,pvc -n minio
    persistentvolume/pv-volume5       5Gi        RWO            Retain           Bound    minio/minio-pvc-minio-0      manual                  38m

    NAME                                      STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/minio-pvc-minio-0   Bound    pv-volume5   5Gi        RWO            manual         39m

Status Bound, very good.   

Next Check status of Pod And StatefulSet:   

    k get pod,statefulset -n minio
    NAME          READY   STATUS    RESTARTS   AGE
    pod/minio-0   1/1     Running   0          14m

    NAME                     READY   AGE
    statefulset.apps/minio   1/1     14m

All is running, looks beautiful.

Let's check SVC: 

    k get svc -n minio
    NAME       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    minio-sc   ClusterIP   10.96.104.83    <none>        9000/TCP         15m

OK, we have ClusterIP type Service, we can reach our application minio inside the cluster, but to check it from the outside, we should create another service, by type of NodePort:

    k expose po minio-0 -n minio --type='NodePort' --port=9000

Now, to connect to the service we should gather NODE_NAME:PORT to connect to it.  we should check where our pod run:

    k get po,svc -n minio -o wide
    NAME          READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
    pod/minio-0   1/1     Running   0          19m   10.35.0.0   worker-2   <none>           <none>

    NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
    service/minio-0    NodePort    10.96.244.231   <none>        9000:30037/TCP   17m   app=minio,controller-revision-hash=minio-64df686db5,statefulset.kubernetes.io/pod-name=minio-0
    service/minio-sc   ClusterIP   10.96.104.83    <none>        9000/TCP         19m   app=minio

The hostname of the node is worker-2, the port is 30037, Let's try to connect to it. [http://worker-2:30037/] As for the credentials we should take MINIO_ROOT_USER, MINIO_ROOT_PASSWORD from our secret. For this task we use:

    MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE
    MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

If you, for some reason lost it, you can always refer to secret file we created for the our application early.

    kubectl get secret minio-secret -n minio -o go-template='{{range $k,$v := .data}}{{"### "}}{{$k}}{{"\n"}}{{$v|base64decode}}{{"\n\n"}}{{end}}'

The output should be:

    ### MINIO_ROOT_PASSWORD
    wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

    ### MINIO_ROOT_USER
    AKIAIOSFODNN7EXAMPLE


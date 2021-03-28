# Create namespace monitoring:   

    kubectl create ns monitoring

# Create service account grabber in that namespace:   

    kubectl create sa grabber -n monitoring

# Create role that can get, list, watch on all Pods in the cluster    

    k create clusterrole --verb=get,list,watch --resource=pods,pods/status grabber-role
    k create clusterrolebinding --clusterrole=grabber-role --user=grabber grabber-role-binding

Since this is cluster role service account grabber will be able to get, list, watch pods everywhere but not to run them:   

    vagrant@master-1:~$ k auth can-i list pods --as grabber
    yes
    
    vagrant@master-1:~$ k auth can-i run pods --as grabber
    no

    vagrant@master-1:~$ k auth can-i watch pods --as grabber -n kube-system
    yes




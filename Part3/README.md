# Complete chart of the simple application from the lecture. It should be configurable with values.

I create new chart.   
To install this chart run helm:

    helm install app-simple .\Chart-Simple\

Execute command from the output, for example:

    export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services app-simple-svc)
    export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")      
    echo http://$NODE_IP:$NODE_PORT

Should return something like this:

    http://192.168.5.11:31264

Open this link to the web-browser.

You can run additional exemplars of application by changing the name of helm chart:

    helm install app-simple1 .\Chart-Simple
    helm install app-simple2 .\Chart-Simple
    helm install app-simple3 .\Chart-Simple

Notice that, all exemplars have their unique object names:
    
    k get svc,cm,po | grep app
    service/app-simple-svc      NodePort    10.96.103.65    <none>        80:32437/TCP   4m8s
    service/app-simple1-svc     NodePort    10.96.214.135   <none>        80:30067/TCP   2m47s
    service/app-simple2-svc     NodePort    10.96.6.102     <none>        80:32584/TCP   2m41s
    service/app-simple3-svc     NodePort    10.96.171.231   <none>        80:30594/TCP   2m35s
    configmap/app-simple-configmap          1      4m8s
    configmap/app-simple1-configmap         1      2m47s
    configmap/app-simple2-configmap         1      2m41s
    configmap/app-simple3-configmap         1      2m35s
    pod/app-simple-depl-7566cdcd76-8z6gf    1/1     Running   0          4m8s
    pod/app-simple1-depl-5479fc998-nwjwq    1/1     Running   0          2m47s
    pod/app-simple2-depl-5b66fc67cb-xvjlm   1/1     Running   0          2m41s
    pod/app-simple3-depl-76b7f8664b-hpx7q   1/1     Running   0          2m35s

Done!


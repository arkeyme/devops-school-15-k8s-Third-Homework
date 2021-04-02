# Complete chart of the simple application from the lecture. It should be configurable with values.

According official Minio documentation, to run Minio we can use docker 

    helm install app-simple .\Chart-Simple\

Execute command from notes, for example:

    export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services app-simple-svc)
    export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")      
    echo http://$NODE_IP:$NODE_PORT

Should return this:

    http://192.168.5.11:31264

Open link to the web-browser.

Done!

# GSP736
>🚨 [PLEASE SUBSCRIBE OUR CHANNEL CLOUDHUSTLER](https://www.youtube.com/@cloudhustlers) 
```cmd
export ZONE=
```
```cmd
gcloud container clusters get-credentials central --zone $ZONE
git clone https://github.com/xiangshen-dk/microservices-demo.git
cd microservices-demo
kubectl apply -f release/kubernetes-manifests.yaml
export EXTERNAL_IP=$(kubectl get service frontend-external | awk 'BEGIN { cnt=0; } { cnt+=1; if (cnt > 1) print $4; }')
curl -o /dev/null -s -w "%{http_code}\n"  http://$EXTERNAL_IP
gcloud logging metrics create Error_Rate_SLI \
    --description="Error rate service level indicator" \
    --log-filter='resource.type="k8s_container" severity=ERROR labels."k8s-pod/app": "recommendationservice"'
```
>Search ```Create alerting policy``` > Select a metric 

>Disable the Show only active resources & metrics

>filter by resource and metric ```Error_Rate```

> Kubernetes Container > Logs-Based Metric > logging/user/Error_Rate_SLI > Apply

> Next > Threshold value ```0.5``` > Disable Use notification channel

>Alter Policy Name ```Error Rate SLI``` > Create Policy


####Uptimerobot

**Deploy a Kubernetes cluster on Google Cloud using the gcloud CLI**

Activate interactive mode

```console
gcloud beta interactive
```

Create a cluster

```console
gcloud container clusters get-credentials demo --zone us-central1-c --project smiling-rhythm-404620
```

![](https://github.com/cipgen/mod5_task2/blob/main/img/create_cluster.png)

Build a container

```console
docker build .
docker tag 3c71867df292 gcr.io/smiling-rhythm-404620/demo:v1.0.0
```

Push to gcr.io

```console
docker push gcr.io/smiling-rhythm-404620/demo:v1.1.0
```

Create a deployment

```console
k create deployment demo --image gcr.io/smiling-rhythm-404620/demo:v1.1.0
```
Configure context with current namespace 'demo'


```console
config set-context --current --namespace demo
```

Check

```console
k get deploy
k get po
k get all
```

Configure LoadBalancer service

```console
k expose deploy demo --port 80 --type LoadBalancer --target-port 8080
```

```console
LB=$(k get svc demo -o jsonpath="{..ingress[0].ip}")
echo $LB
Version: 1.1.0

curl $LB
Version 1.1.0
```
![](https://github.com/cipgen/mod5_task2/blob/main/img/mon_01.png)

####Task 2


Build a container with a new version
```console
docker build . -t gcr.io/smiling-rhythm-404620/demo:v2.1.0
```
Push to gcr.io

```console
docker push gcr.io/smiling-rhythm-404620/demo:v2.1.0
```
Create annotations for demo

```console
k annotate deploy demo kubernetes.io/change-cause="Add v1.1.0"
```
Deploy the second version

```console
k create deployment demo2 --image gcr.io/smiling-rhythm-404620/demo:v2.1.0
```

Create annotations for demo2

```console
k annotate deploy demo2 kubernetes.io/change-cause="Add v2.1.0"
```
Redirect traffic from the first to the second version using Canary (25%) and Blue


```console
k edit svc demo
k get po -Lapp,run
```

```console
Scale deployment demo --replicas 6
Scale deployment demo2 --replicas 2
```

![](https://github.com/cipgen/mod5_task2/blob/main/img/mon_d1_d2.png)


Delete the cluster

```console
gcloud container clusters delete demo --zone us-central1-c
```


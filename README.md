# dashboard-k8s

## Learn how to configure the access to microk8s' dashboard

We assume that `microk8s` is already installed on your system and you are using a `Linux` server.

## Check if dashboard is available

Run the following command to see if `dashboard` is available.

```bash
microk8s status
```

You should get something like this:

```bash
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ...
  disabled:
    ...
    dashboard            # (core) The Kubernetes dashboard
    ...
```

## Enable the dashboard

Run the following command to enable the addon dashboard:

```bash
microk8s enable dashboard
```

You should get something like this:

```bash
Infer repository core for addon dashboard
Addon core/dashboard is enabled
```

## Check if the dashboard is running

Run the following command:

```bash
microk8s kubectl -n kube-system get svc
```

You should see the service for dashboard.

```bash
NAME                   TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
kubernetes-dashboard   ClusterIP   10.20.30.40   <none>        443/TCP   1h
```

## Make the dashboard accessible at all times

Run the following commands using the file `k8s-dashboard-lb.yaml` from this repo.

```bash
microk8s kubectl apply -f k8s-dashboard-lb.yml
microk8s kubectl -n kube-system get svc
```

You should see the new service and at which port is exposed:

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
k8s-dashboard-lb   LoadBalancer   10.152.183.81   <pending>     443:30123/TCP   1m
```

Now you can access it at `https://127.0.0.1:30123`.

## Authentication

To be able to login in dashboard you need to create a token with the following command:

```bash
microk8s kubectl create token default > k8s-token.txt
```

The token will be saved in `k8s-token.txt` file. You need to copy it from there and use it in the browser.

## Using nginx as proxy

You can use the `nginx.conf` file as example how to access the dashboard from internet using `nginx` as web server.

Replace `k8s.<YOUR-DOMAIN>` with your hostname and `<MAPPED_PORT_443>` with the exposed port (in our example was **30123**).

Don't forget also to enable the HTTPS for it.

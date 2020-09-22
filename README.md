# TL;DR

Build the Docker image for Jenkins:

```bash
docker build -t localhost:32000/jenkins .
docker push localhost:32000/jenkins
```

Deploy Jenkins:

```bash
cd k8s
kubectl create ns jenkins
kubectl apply -f jenkins-deployment.yaml
kubectl apply -f jenkins-service.yaml
kubectl apply -f jenkins-ingress.yaml
```

Patch the host used in the ingress for Jenkins, using [xip.io](http://xip.io/) to resolve custom domain names used for testing:

```bash
HOST=$(ip -o route get to 8.8.8.8 | sed -n 's/.*src \([0-9.]\+\).*/\1/p').xip.io

kubectl patch ingress jenkins -n jenkins --type='json' -p="[{\"op\": \"replace\", \"path\": \"/spec/rules/0/host\", \"value\":\"${HOST}\"}]"
```

If you were to use the [NGINX Ingress](https://kubernetes.github.io/ingress-nginx/) instead of the `ingress` addon, you'd have to change the first command with:

```bash
HOST=$(kubectl get svc ingress-nginx-controller -n ingress-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}').xip.io
```

Retrieve the password to unlock Jenkins with:

```bash
kubectl -n jenkins exec $(kubectl get pod -n jenkins -l app=jenkins --no-headers -o=custom-columns='DATA:.metadata.name') -- cat /var/jenkins_home/secrets/initialAdminPassword
```

Get the kube config required to create the `kubeconfig` credentials used by the `kubernetes-cd` plugin in Jenkins:

```bash
microk8s config
```

The actual config file is stored here: */var/snap/microk8s/current/credentials/client.config*

# Known issues

At the moment the continuous deployment pipeline is failing as per [this issue report](https://github.com/jenkinsci/kubernetes-cd-plugin/issues/134).


Create account on gitlab.com
> git clone https://github.com/GoogleCloudPlatform/microservices-demo

> cd microservices-demo

> git remote add gitlab git@gitlab.com:yakw/microservices-demo.git

> git remote remove origin

> git push gitlab master


push all need images to Docker Hub (yerlanagzhigitov repo) using Hack script


> kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml

> helm repo add fluxcd https://charts.fluxcd.io


> kubectl create namespace flux

> helm upgrade --install flux fluxcd/flux -f flux.values.yaml --namespace flux

Release "flux" does not exist. Installing it now.

NAME: flux

LAST DEPLOYED: Thu Apr  2 19:52:07 2020

NAMESPACE: flux

STATUS: deployed

REVISION: 1

TEST SUITE: None

NOTES:

Get the Git deploy key by either (a) running


  kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2


or by (b) installing fluxctl through

https://docs.fluxcd.io/en/latest/references/fluxctl.html#installing-fluxctl

and running:


  fluxctl identity --k8s-fwd-ns flux


> helm upgrade --install helm-operator fluxcd/helm-operator -f helm-operator.values.yaml --namespace flux

Release "helm-operator" does not exist. Installing it now.

NAME: helm-operator

LAST DEPLOYED: Thu Apr  2 20:25:18 2020

NAMESPACE: flux

STATUS: deployed

REVISION: 1

TEST SUITE: None

NOTES:

Flux Helm Operator docs https://docs.fluxcd.io


Example:


AUTH_VALUES=$(cat <<-END

usePassword: true

password: "redis_pass"

usePasswordFile: true

END

)


> kubectl create secret generic redis-auth --from-literal=values.yaml="$AUTH_VALUES"

cat <<EOF | kubectl apply -f -

apiVersion: helm.fluxcd.io/v1

kind: HelmRelease

metadata:

  name: redis

  namespace: default

spec:

  releaseName: redis

  chart:

    repository: https://kubernetes-charts.storage.googleapis.com

    name: redis

    version: 9.0.2

  valuesFrom:

  - secretKeyRef:

      name: redis-auth

  values:

    master:

      persistence:

        enabled: false

    volumePermissions:

      enabled: true

    metrics:

      enabled: true

    cluster:

      enabled: false

EOF


fluxctl install

fluxctl identity --k8s-fwd-ns flux --add key to Gitlab


> cat deploy/namespaces/microservices-demo.yaml

apiVersion: v1

kind: Namespace

metadata:

  name: microservices-demo

> kubectl logs flux-6769997fb4-m57dc -n flux --tail 1

ts=2020-04-05T13:05:25.016712024Z caller=sync.go:594 method=Sync cmd="kubectl apply -f -" took=219.264919ms err=null output="namespace/microservices-demo unchanged"


> kubectl get helmrelease -n microservices-demo

> helm list -n microservices-demo


Install Istio and istioctl

change frontend and build new docker imgae with tag v0.0.2 and push to docker hub

> helm history frontend -n microservices-demo

REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION

1               Mon Apr  6 13:15:44 2020        superseded      frontend-0.21.0 1.16.0          Install complete

2               Mon Apr  6 13:55:33 2020        deployed        frontend-0.21.0 1.16.0          Upgrade complete


> helm history frontend -n microservices-demo

ts=2020-04-06T14:18:33.61687521Z caller=helm.go:69 component=helm version=v3 info="preparing upgrade for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.184271655Z caller=helm.go:69 component=helm version=v3 info="performing update for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.203724282Z caller=helm.go:69 component=helm version=v3 info="creating upgraded release for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.22812111Z caller=helm.go:69 component=helm version=v3 info="checking 4 resources for changes" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.250807225Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Service \"frontend\"" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.283089048Z caller=helm.go:69 component=helm version=v3 info="Created a new Deployment called \"frontend-hipster\" in microservices-demo\n" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.332515725Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Gateway \"frontend-gateway\"" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.471564288Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for VirtualService \"frontend\"" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.498431086Z caller=helm.go:69 component=helm version=v3 info="Deleting \"frontend\" in microservices-demo..." targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.543721737Z caller=helm.go:69 component=helm version=v3 info="updating status for upgraded release for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:18:34.74067194Z caller=release.go:266 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="Helm release sync succeeded" revision=480d32f15131f3fc8852eaaeb51fcc49c568f5f0
ts=2020-04-06T14:19:03.572284686Z caller=operator.go:307 component=operator info="enqueuing release" resource=microservices-demo:helmrelease/frontend
ts=2020-04-06T14:19:03.875089643Z caller=release.go:360 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="performing dry-run upgrade to see if release has diverged"
ts=2020-04-06T14:19:03.877995435Z caller=helm.go:69 component=helm version=v3 info="preparing upgrade for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:19:04.344769204Z caller=helm.go:69 component=helm version=v3 info="performing update for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:19:04.355779585Z caller=helm.go:69 component=helm version=v3 info="dry run for frontend" targetNamespace=microservices-demo release=frontend
ts=2020-04-06T14:19:04.36056303Z caller=release.go:404 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="no changes" action=skip

> export FLUX_FORWARD_NAMESPACE=flux

> fluxctl list-workloads -a

> fluxctl list-images -n microservices-demo


push all helmreleases to gitlab


> helm repo add flagger https://flagger.app

> kubectl apply -f https://raw.githubusercontent.com/weaveworks/flagger/master/artifacts/flagger/crd.yaml

> helm upgrade --install flagger flagger/flagger --namespace=istio-system --set crd.create=false --set meshProvider=istio --set metricsServer=http://prometheus:9090


add istio-injection: enabled in ns microservices-demo

> kubectl get ns microservices-demo --show-labels

> kubectl delete pods --all -n microservices-demo

> kubectl describe pod -l app=frontend -n microservices-demo

> kubectl get gateway -n microservices-demo

> kubectl get svc istio-ingressgateway -n istio-system

> kubectl get canary -n microservices-demo

> kubectl describe canary frontend -n microservices-demo

> kubectl get pods -n microservices-demo -l app=frontend-primary


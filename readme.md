# ARGO CD

ArgoCD instalación y configuración

## Documentación

    Videotutorial de argo: https://www.youtube.com/watch?v=eyk5oKK6rDM
    Tutorial de argo: https://blog.baeke.info/2019/12/25/giving-argo-cd-a-spin/
    Repo de tutorial: https://github.com/gbaeke/argo-demo
    Videotutorial de argo: https://www.youtube.com/watch?v=0WAm0y2vLIo
    Obtener password de argo: https://stackoverflow.com/questions/68297354/what-is-the-default-password-of-argocd
    Repo de argocd: https://github.com/argoproj/argocd-example-apps

## Instalación de ArgoCD

    kubectl create namespace argocd

    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

### Instalación de Argo CLI

    brew tap argoproj/tap

    brew install argoproj/tap/argocd

### Configuración

Hacer un port-foward y en google chrome escribir thisisunsafe

    kubectl port-forward svc/argocd-server -n argocd 8080:443

Obtención del password de admin

    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

Usuario: admin
Password: XLURzR9ZDb3eUUmK

Cambiar password:

    argocd login localhost:8080 
    argocd account update-password

## Crear App en ArgoCD

Repositorio que voy a utilizar (gitops):

    https://github.com/hernyer/argo-demo.git

### UI ArgoCD

    NEW APP
    Application Name: guestbook2
    Project: default
    SYNC POLICY: Automatic
    Repository URL: https://github.com/hernyer/argo-demo.git
    Path: .
    Cluster URL: https://kubernetes.default.svc
    Namspace: argo-demo2

## Actualizar App

Sync app y delete POD.

## Ejemplo de APP

guestbook-ui-deployment.yaml

La app corré en <http://localhost:80/>

El image: gcr.io/heptio-images/ks-guestbook-demo:0.2 tiene dos versiones para comparar el funcionamiento.

    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: guestbook-ui
    spec:
    replicas: 1
    revisionHistoryLimit: 3
    selector:
        matchLabels:
        app: guestbook-ui
    template:
        metadata:
        labels:
            app: guestbook-ui
        spec:
        containers:
        - image: gcr.io/heptio-images/ks-guestbook-demo:0.2
            name: guestbook-ui
            ports:
            - containerPort: 80

guestbook-ui-svc.yaml

Agregando el loadbalancer permite acceder por localhost.

    apiVersion: v1
    kind: Service
    metadata:
    name: guestbook-ui
    spec:
    ports:
    - port: 80
        targetPort: 80
    selector:
        app: guestbook-ui
    type: LoadBalancer

![Logo of the project](https://raw.githubusercontent.com/jehna/readme-best-practices/master/sample-logo.png)

# NSDL Edastakhat
> Esign Everywhere

A brief description of your project, what it is used for and how does life get
awesome when someone starts to use it.

## Installing / Getting started

Install Docker and Microk8s Kubernetes Single Node Cluster

```shell
sudo snap install microk8s --classic
```

Above command will install docker and kubernetes cluster.

### Initial Configuration

Require some initial configuration after installation cluster.
create docker.json inside /etc/docker/docker.json
```shell
{"insecure-registries" : ["127.0.0.1:32000", "172.19.74.42:32000", "172.30.151.90:32000"]}
```
Edit Registry file inside microk8s cluster
edit /var/snap/microk8s/current/args/containerd-template.toml and add the following under [plugins] -> [plugins."io.containerd.grpc.v1.cri".registry]

```shell
[plugins."io.containerd.grpc.v1.cri".registry.mirrors."172.19.74.42:32000"]
          endpoint = ["http://172.19.74.42:32000"]
[plugins."io.containerd.grpc.v1.cri".registry.mirrors."127.0.0.1:32000"]
          endpoint = ["http://127.0.0.1:32000"]
```
execute below command
```
microk8s stop
microk8s start
```
## Setup K8s Single Node Microk8s Cluster

Follow Order form this step:

```shell
alias kubectl='microk8s.kubectl'
microk8s enable rbac
microk8s enable dns:172.19.65.167
microk8s enable registry:size=40Gi
microk8s enable traefik
```

And state what happens step-by-step.

## Create namespace
```shell
kubectl create namespace db
kubectl create namespace dt
```
db namespace will be used for Postgres db and Activemq
dt namspace will be used for Zipkin,Grafana and Prometheus

```shell

```

## Setup traefik Edge Router

Below commands will install traefik role based access, custom resource defination, ingress routes for edastakhat.
Middlleware for handling traefik routes.

```shell
kubectl apply -f /ed-k8s/deploy/dev/traefik/traefik-rbac.yml
kubectl apply -f /ed-k8s/deploy/dev/traefik/traefik-crd.yml
kubectl apply -f /ed-k8s/deploy/dev/traefik/ed-ingress.yml
kubectl apply -f /ed-k8s/deploy/dev/traefik/ed-middleware.yml
```
After installation of traefik, edit traefik daemonsets and add below config.
```shell
      --providers.kubernetesingress=false
      --providers.kubernetesingress.ingressendpoint.ip=127.0.0.1
      --log=true
      --log.level=INFO
      --accesslog=true
      --accesslog.filepath=/dev/stdout
      --accesslog.format=json
      --entrypoints.web.address=:80
      --entrypoints.websecure.address=:8443
      --entrypoints.postgres.address=:5432
      --providers.kubernetescrd
      --api.dashboard=true
      --api
      --api.insecure
```

### Install traefik dashboard
```
kubectl apply -f /ed-k8s/deploy/dev/traefik/traefik-dashboard.yml
```

What's traefik dashboard?
* What's the main functionality
* You can also do another thing
* If you get really randy, you can even do this

### Install Edastakhat Resources

The Edastakhat Project.
```
kubectl apply -f /ed-k8s/deploy/dev/ed-global-cm.yml
kubectl apply -f /ed-k8s/deploy/dev/ed-secrets.yml
kubectl apply -f /ed-k8s/deploy/dev/ed-postgres-secrets.yml
kubectl apply -f /ed-k8s/deploy/dev/ed-postgres-pv-pvc.yml
```

#### Argument 1
Type: `String`  
Default: `'default value'`

State what an argument does and how you can use it. If needed, you can provide
an example below.

Example:
```bash
awesome-project "Some other value"  # Prints "You're nailing this readme!"
```

#### Argument 2
Type: `Number|Boolean`  
Default: 100

Copy-paste as many of these as you need.

## Contributing

When you publish something open source, one of the greatest motivations is that
anyone can just jump in and start contributing to your project.

These paragraphs are meant to welcome those kind souls to feel that they are
needed. You should state something like:

"If you'd like to contribute, please fork the repository and use a feature
branch. Pull requests are warmly welcome."

If there's anything else the developer needs to know (e.g. the code style
guide), you should link it here. If there's a lot of things to take into
consideration, it is common to separate this section to its own file called
`CONTRIBUTING.md` (or similar). If so, you should say that it exists here.

## Links

Even though this information can be found inside the project on machine-readable
format like in a .json file, it's good to include a summary of most useful
links to humans using your project. You can include links like:

- Project homepage: https://your.github.com/awesome-project/
- Repository: https://github.com/your/awesome-project/
- Issue tracker: https://github.com/your/awesome-project/issues
  - In case of sensitive bugs like security vulnerabilities, please contact
    my@email.com directly instead of using issue tracker. We value your effort
    to improve the security and privacy of this project!
- Related projects:
  - Your other project: https://github.com/your/other-project/
  - Someone else's project: https://github.com/someones/awesome-project/


## Licensing

One really important part: Give your project a proper license. Here you should
state what the license is and how to find the text version of the license.
Something like:

"The code in this project is licensed under MIT license."

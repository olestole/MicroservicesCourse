# Blog
Part of course in [*Microservices with Node JS and React*](https://www.udemy.com/course/microservices-with-node-js-and-react/).

This repo consists of multiple services that,when run concurrently, fulfills the needs of a blog-app that allows creation of posts and comments.

## Development

Every service is dockerized and we use *Kubernetes* in order to manage the containerized applications. In order to create and then apply the configuration to the resources we use [*Skaffold*](https://skaffold.dev/).

```bash
$ cd ~/infra/k8s

# Without skaffold. Applying all the configuration
$ kubectl apply -f .

# With skaffold:
$ skaffold dev
```

### Perks of using Skaffold
- With the correct setup, Skaffold applies changes to files on the fly. You don't have to rebuild a docker-image, push it and restart the kubernetes pod with `kubectl rollout restart deployment posts-depl`
- When you quit `skaffold dev`, it takes down all the pods it spun up - saves you the manual destroy.

### Local redirect for Kubernetes
In order to run the services locally you'll have to add a redirect from `posts.com` to `localhost`. This is done as part of setting up the *ingress service* `ingress-srv.yaml`. This way every network-request can be sent to `posts.com` and be handled by the ingress service which will route the request to the correct service based **only on the route destination**.

```bash
# /etc/hosts
...

127.0.0.1 posts.com
```

## Lessons from project
- The big challenge in microservices is data
- There are different way sof sharing data. We are going to focus on async communication.
- Async communication focuses communicating changes using *events* sent to an *event-bus*
- Async communication encourages each service to be 100% self-sufficient. Relatively easy to handle temporary downtime or new service creation.
- Docker makes it easy to package up services
- Kubernetes is a pain to setup, but makes it really easy to deploy + scale services.
- Create an alias for `kubectl`: `alias k=kubectl`

### Useful Kubernetes commands
```bash
# Print out information about all the running pods
$ kubectl get pods

# Execute the given command in a running pod
$ kubectl eec -it [pod_name] [cmd]

# Print out logs from the given pod
$ kubectl logs [pod_name]

# Delete the given pod
$ kubectl delete pod [pod_name]

# Tell kubernetes to process the configuration
$ kubectl apply -f [config file_name]

# Print useful information about the running pod
$ kubectl describe pod [pod_name]

# Restart a pod. E.g. after having rebuilt and pushed a Docker image to dockerhub
$ kubectl rollout restart [pod_type] [pod_name]
$ kubectl rollout restart deployment posts-depl
```
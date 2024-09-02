# grapple-template


## getting started

```
k create secret generic externaloperations-options --from-literal ExternalOperations="/tmp/externaloperations/options.json" --from-literal LICENSESPRING_AUTHORIZATION='Api-Key ${LICENSE_SPRING_API_KEY_FROM_1PASSWORD}'
```



deactivated: (not necessary yet)
```
k create secret -n grpl-system generic onepassword-connect-token --from-file /Users/patrickriegler/code/grapple/grsf-license-function/1password-credentials.json
```

```
kubectl apply -f - <<EOF
---
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: staging
spec:
  provider:
    onepassword:
      connectHost: https://onepassword-connect-staging
      vaults:
        staging: 1  # look in this vault first
        shared: 2   # next look in here. error if not found
      auth:
        secretRef:
        connectTokenSecretRef:
          name: onepassword-connect-token-staging
          key: token
```


### dev mode - using docker-compose




### dev mode in kubernetes
    skaffold dev --port-forward 

> [!TIP]
> skaffold dev --port-forward --default-repo=${YOUR_DOCKER_USERNAME} 
> (add --default-repo=${YOUR_DOCKER_USERNAME} if not running locally)
> ${YOUR_DOCKER_USERNAME} = e.g. grpl from grpl/app:latest (user name in docker hub) - a docker user name where you have permissions to write

> [!NOTE]
> further information about skaffold:
> https://skaffold.dev/docs/quickstart/


## working with the front-end application

### run the application locally
    pnpm i 
    pnpm start

> [!TIP]
> run:
> npm install -g pnpm
> (if pnpm is not installed yet)


### build and run using docker
    docker build -t grapple-template .
    docker run --rm -p 8080:80 grapple-template

then open:
http://localhost:8080



### build and run using skaffold (locally)
    minikube start
    skaffold dev --port-forward

> [!TIP]
> a message in the log with the port-forward appears:
> *Port forwarding service/myapp in namespace default, remote port 80 -> http://127.0.0.1:4503*


### build and run using skaffold (in a remote cluster)
    skaffold dev --default-repo=${YOUR_DOCKER_USERNAME} 

> [!TIP]
> ${YOUR_DOCKER_USERNAME} = e.g. grpl from grpl/app:latest (user name in docker hub) - a docker user name where you have permissions to write

## folder structure

    .                   -> application base directory
    ├── src                 -> application src directory
        ├── index.html          -> 
        ├── index.ts            -> 
        ├── App.svelte          -> 
    ├── grapi               -> grapi (grapple API) injections
        ├── controllers          -> grapi controllers
            ├── ping2.controller.ts     -> example controller injection
    ├── gruim               -> gruim (grapple UI modules) injections
        ├── shared          -> gruim shared UI modules 
            ├── *.svelte    -> example gruim UI module injection
    ├── chart               -> helm chart directory ==> https://helm.sh/docs/topics/charts/
        ├── values.yaml         -> the default configuration values for this chart
        ├── values*.yaml        -> values files for different profiles / environments
        ├── Chart.yaml          -> A YAML file containing information about the chart
        ├── templates           -> folder for all yaml manifest templates
        ├── charts              -> folder for sub-charts
    ├── test                -> application test cases directory
        ├── *.sh                -> test cases
    ├── .dockerignore       -> You can use a .dockerignore file to exclude files or directories from the build context.
    ├── .gitignore          -> Specifies intentionally untracked files to ignore
    ├── Dockerfile          -> Dockerfile is a text document containing all the commands the user requires to call on the command line to assemble an image.
    ├── Dockerfile.test     -> Dockerfile for the test cases (optional)
    ├── package.json        -> Dockerfile for the test cases (optional)
    ├── README.md           -> this file
    ├── skaffold.yaml       -> Skaffold contains configuration for all phases of the application delivery process
    ├── webpack.config.js   -> webpack configuration file ==> https://webpack.js.org/concepts/configuration/


## environment variables

APP: define the name of the APP
NAMESPACE: define a namespace extension to the deplyoment

SKAFFOLD_CACHE_ARTIFACTS: "false" - to disable cached artifacts

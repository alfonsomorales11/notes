# Docker & Minikube

## Container IP adddress
```
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
```


## IP ADDRESSES
```
agente - 172.21.0.2

nexus - 172.21.0.3

jenkins - 172.21.0.4

sonar - 172.21.0.5
```


## SSH to agent
```
ssh root@172.21.0.2
```

------------------------------------------------------------------------------------------------------

## Connect container to network
```
docker network connect sonar_sonarnet container
```

------------------------------------------------------------------------------------------------------

## Run nexus container
```
docker run -d --name nexus -p 8081:8081 -p 8082:8082 -p 8083:8083 sonatype/nexus3:latest
```

## Run ssh agent docker
```
docker run -it -d -p 22:22 --name agente -v "//var/run/docker.sock:/var/run/docker.sock" agente
```

------------------------------------------------------------------------------------------------------

## Docker daemon.json
```
{
  "insecure-registries": [
    "172.21.0.6:8083",
    "172.21.0.6:8082"
  ]
}
```


## Authenticate insecure registry
```
docker login -u admin localhost:8082

docker login -u admin 192.168.100.124:8082
```


## Tag image
```
docker tag <IMAGE_NAME> localhost:8082/stride-webapp:<VERSION>
```


## Push image
```
docker push localhost:8082/stride-webapp:<VERSION>
```

------------------------------------------------------------------------------------------------------

## Start minikube
```
minikube start --insecure-registry="192.168.100.124:8082"
```


## Create secret manually
```
kubectl create secret docker-registry regcred --docker-server="http://192.168.100.124:8082" --docker-username="admin" --docker-password="admin"
```

----------------------------------------------------------------------------------
# helm commands

## Package a helm chart
```
helm package stride-chart
```


## Move generated .tgz file to charts folder
```
mv ./stride-chart/stride-chart-0.1.1.tgz ./charts/
```


## Generate index.yaml file
```
helm repo index .
```


## Add a new helm repository
```
helm search repo alfonsomorales11
```


## Search for available charts in a repository
```
helm search repo alfonsomorales11
```


## Install helm chart as it is
```
helm install stride  alfonsomorales11/stride-chart
```


## Install helm chart & override values
```
helm install stride --set deploymentName="mydeploy" alfonsomorales11/stride-chart
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Install helm chart
```
helm install stride alfonsomorales11/stride-chart --set-string namespace.name=$NAMESPACE --set-string deploymentName=$DEPLOYMENT_NAME --set-string repositoryURL=$NEXUS_URL --set-string dockerConfig.value=$DOCKER_CONFIG_VALUE --set-string image.name=$IMAGE_NAME --set-string image.tag=$BUILD_NUMBER --set-string secretName=$SECRET_NAME
```

## Upgrade already installed helm chart
```
helm upgrade stride alfonsomorales11/stride-chart --set-string namespace.name=$NAMESPACE --set-string deploymentName=$DEPLOYMENT_NAME --set-string repositoryURL=$NEXUS_URL --set-string dockerConfig.value=$DOCKER_CONFIG_VALUE --set-string image.name=$IMAGE_NAME --set-string image.tag=$BUILD_NUMBER --set-string secretName=$SECRET_NAME
```
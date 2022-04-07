# redis-counter-app
redis-counter-app with containerized 

#Build Instructions

mvn clean install

docker login --username <username> --password <pass>
  
  
docker build . -t <REGISTRY>/redis-counter-app:v1 --no-cache
  
  
docker push  <REGISTRY>/redis-counter-app:v1
  
If you are on minikube then run
  
kubectl apply -f ./manifests/*.yml

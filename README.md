# Kubernetes-HPA
Horizontal Pod Autoscaling in Kubernetes (minikube) <br/>
* In Kubernetes, a HorizontalPodAutoscaler automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand.<br/>
* This method can also be referred to as scaling out. In this method, Kubernetes allows DevOps engineer, SRE, or your cluster admin to increase or decrease the number of pods automatically based upon application resource usage. The below diagrams explains the process.<br/><br/>
![image](https://user-images.githubusercontent.com/92582005/204075069-1855a129-bf24-4bb8-8b99-5d0b4b6b06c3.png) <br/><br/>
![image](https://user-images.githubusercontent.com/92582005/204074904-19404232-0f72-4c9a-a9bf-4e291ff56670.png) <br/>
### Steps for HPA demo. <br/>
* Enable the metrics-server addon for minikube. <br/>
  $ minikube addons enable metrics-server <br/>
* It’s helpful to use lower collection intervals of 1 minute and 10 seconds to see more immediate action.<br/>
  $ minikube start — extra-config=controller-manager.horizontal-pod-autoscaler-upscale-delay=1m — extra-config=controller-manager.horizontal-pod-autoscaler-downscale-delay=1m — extra-config=controller-manager.horizontal-pod-autoscaler-sync-period=10s — extra-config=controller-manager.horizontal-pod-autoscaler-downscale-stabilization=1m <br/>

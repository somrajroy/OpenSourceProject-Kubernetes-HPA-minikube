## Kubernetes-Horizental Pod Autoscaler/Autoscaling
Horizontal Pod Autoscaling in Kubernetes (minikube). Ideally HPA needs to be done in EKS/AKS with CA which will be done later. <br/>
* In Kubernetes, a HorizontalPodAutoscaler automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand. <br/>
* Kubernetes uses the horizontal pod autoscaler (HPA) to monitor the resource demand and automatically scale the number of replicas. <br/>
* This method can also be referred to as scaling out. In this method, Kubernetes allows DevOps engineer, SRE, or cluster admin to increase or decrease the number of pods automatically based upon application resource usage. The below diagrams explains the process.<br/><br/>
![image](https://user-images.githubusercontent.com/92582005/204075069-1855a129-bf24-4bb8-8b99-5d0b4b6b06c3.png) <br/><br/>
![image](https://user-images.githubusercontent.com/92582005/204074904-19404232-0f72-4c9a-a9bf-4e291ff56670.png) <br/>
### Steps for HPA demo. <br/>
* Enable the metrics-server addon for minikube by running the below command (Confirmation will be like the image below). The metrics-server fetches resource metrics from the kubelets and exposes them in the Kubernetes API server through the Metrics API for use by the HPA and VPA. By default, the horizontal pod autoscaler checks the Metrics API every 15 seconds for any required changes in replica count, but the Metrics API retrieves data from the Kubelet every 60 seconds. Effectively, the HPA is updated every 60 seconds. When changes are required, the number of replicas is increased or decreased accordingly. <br/>
  $ minikube addons enable metrics-server <br/>
  ![image](https://user-images.githubusercontent.com/92582005/204088470-c04815e8-b5b9-48b1-ba8d-f7d70258f3e4.png) <br/>
* It’s helpful to use lower collection intervals of 1 minute and 10 seconds to see more immediate action.<br/>
  $ minikube start — extra-config=controller-manager.horizontal-pod-autoscaler-upscale-delay=1m — extra-config=controller-manager.horizontal-pod-autoscaler-downscale-delay=1m — extra-config=controller-manager.horizontal-pod-autoscaler-sync-period=10s — extra-config=controller-manager.horizontal-pod-autoscaler-downscale-stabilization=1m <br/>
* Download and deploy the manifest HPA.yaml <br/>
  $ kubectl apply -f HPA.yaml <br/>
* Check the pod and service created. Wait for the pod to be in running state <br/>
  $ kubectl get pod,svc <br/>
  ![image](https://user-images.githubusercontent.com/92582005/204087144-6d7b705c-0556-47c1-8ff0-fd07ab3fd609.png) <br/>
* Create the HorizontalPodAutoscaler. <br/>
  $ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=5 <br/>
* Check the current status of the newly-made HorizontalPodAutoscaler, by running below command. The output should be similar to the image. <br/>
  $ kubectl get hpa <br/>
  ![image](https://user-images.githubusercontent.com/92582005/204087417-9d3dd80f-2fc6-460c-881c-8649ee43bc19.png) <br/>
* Check how the autoscaler reacts to increased load. To do this, start a different Pod to act as a client. The container within the client Pod runs in an infinite loop, sending queries to the php-apache service. Open a new window and run the below command. <br/>
  $ kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done" <br/>
* In main window run the below command. (type Ctrl+C to end the watch). Within a minute or so, the terminal should display the higher CPU load like the image below<br/>
  $ kubectl get hpa php-apache --watch <br/>
  ![image](https://user-images.githubusercontent.com/92582005/204087674-17e8a7d3-0492-4b44-ad98-0bb65ee2b7ab.png) <br/>
* After the load increased the deployment size will also increase and can be seen by running below command.<br/>
  $ kubectl get deployment php-apache <br/>
  ![image](https://user-images.githubusercontent.com/92582005/204087790-fbc727b5-6829-4494-a369-27349ee33b20.png) <br/>
* After few moments if the pods are checked then all 5 pods will be running. (replica count matching the figure from the HorizontalPodAutoscaler) <br/>
  $ kubectl get pods <br/>
  ![image](https://user-images.githubusercontent.com/92582005/204087844-804a1d9b-d455-4462-8faa-c82a85178f67.png) <br/>
* Stoping the load - In the terminal where the Pod that runs a busybox image, terminate the load generation by typing Control + C. <br/>
* After few mins if we check the deployment and replicas then we can see only 1 pod with 1 replica.
  
 #### Further references<br/>
* [How do I set up Kubernetes Metrics Server and Horizontal Pod Autoscaler on Amazon EKS?](https://aws.amazon.com/premiumsupport/knowledge-center/eks-metrics-server-pod-autoscaler/)<br/>
* [Scale applications in Azure Kubernetes Service (AKS)](https://learn.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale?tabs=azure-cli)<br/>
* [Scaling options for applications in Azure Kubernetes Service (AKS)](https://learn.microsoft.com/en-us/azure/aks/concepts-scale)<br/>

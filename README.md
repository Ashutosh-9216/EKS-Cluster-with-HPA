
# 🚀 AWS EKS: HPA Setup with Load Testing

The **goal of this project** is to:

---

## 🎯 **Demonstrate Auto-Scaling in Kubernetes on AWS EKS**

This project shows how to:

### ✅ 1. **Deploy an application** on an AWS-managed Kubernetes cluster (EKS).

### ✅ 2. **Expose the app** to the internet using a **LoadBalancer service**, so users can access it externally.

### ✅ 3. **Install and use Metrics Server** to collect CPU usage data from pods.

### ✅ 4. **Configure Horizontal Pod Autoscaler (HPA)** to automatically:

* Monitor the app’s CPU usage
* Increase or decrease the number of pods
* Based on real-time CPU demand

### ✅ 5. **Generate traffic** to simulate high CPU load and **observe the autoscaling in action**.

---



## 🔹 Step 1: Create EKS Cluster

```bash
eksctl create cluster --name my-cluster-1 --region ap-northeast-1
```
<img width="1345" height="596" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20190031.png?raw=true" />

---

## 🔹 Step 2: Deploy the Application

Apply your deployment:

```bash
kubectl apply -f deployment.yaml
```
<img width="1345" height="596" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20191340.png?raw=true" />


---

## 🔹 Step 3: Expose Deployment as LoadBalancer

```bash
kubectl expose deployment mywebd --type=LoadBalancer --name=mywebd-svc --port=80
```

Get the service and copy the `EXTERNAL-IP`:

```bash
kubectl get svc mywebd-svc
```
<img width="1267" height="95" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20191435.png?raw=true" />


Open in browser or use curl:

```
http://<EXTERNAL-IP>
```
<img width="936" height="390" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20191510.png?raw=true" />

---

## 🔹 Step 4: Install Metrics Server (for HPA)

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```



Check if metrics-server pod is running:

```bash
kubectl get pods -n kube-system
```


Test metrics availability:

```bash
kubectl top pods
```
<img width="652" height="48" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20191556.png?raw=true" />


---

## 🔹 Step 5: Create Horizontal Pod Autoscaler

```bash
kubectl autoscale deployment mywebd --cpu-percent=50 --min=1 --max=10
```

Check HPA:

```bash
kubectl get hpa
```


Watch live scaling:

```bash
kubectl get hpa -w
```
<img width="757" height="274" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20192515.png?raw=true" />

---

## 🔹 Step 6: Generate Load (in Separate Terminal 1)

```bash
kubectl run -i --tty load-generator --rm --image=busybox /bin/sh
```

Then inside the pod:

```sh
while true; do wget -q -O- http://<EXTERNAL-IP>; done
```
<img width="639" height="546" alt="image" src="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20193012.png?raw=true" />

> Replace `<EXTERNAL-IP>` with your actual LoadBalancer IP.


## Watch live scaling:

```bash
kubectl get hpa -w
```
<img width="877" height="247" alt="https://github.com/Ashutosh-9216/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20193040.png?raw=true" />


---

## ✅ Expected Behavior

* `kubectl get hpa -w` shows increasing `TARGETS` CPU usage.
* `REPLICAS` increase as load increases.
* HPA scales from 1 to more pods automatically.

---

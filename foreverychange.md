# After every change in backend use these commands

To apply the changes you've made to your backend application and reflect them in your EKS cluster, follow these steps:

### 1. **Build Your Docker Image**

Rebuild your Docker image to include the updated code:

```bash
docker buildx build --platform linux/amd64 -t soboards/billboardserver:latest .
```

### 2. **Push the Docker Image to Your Registry**

Push the newly built image to your Docker registry:

```bash
docker push soboards/billboardserver:latest
```

### 3. **Update Your Kubernetes Deployment**

You need to apply the changes in your Kubernetes deployment. If your deployment YAML file is already set to use the latest image tag, you can simply trigger a rollout:

```bash
kubectl rollout restart deployment soboards-backend-deployment -n soboards
```

### 4. **Verify the Update**

After restarting the deployment, check the status of your pods to ensure they are running the latest version:

```bash
kubectl get pods -n soboards
```

You can also check the logs of a specific pod to verify that the new changes are applied:

```bash
kubectl logs <pod-name> -n soboards
```

### 5. **Test Your API**

Finally, test your API at `https://api.soboards.com` to ensure that everything is working correctly with the new CORS settings.

### Summary

1. Build and push the updated Docker image.
2. Restart your Kubernetes deployment to use the new image.
3. Verify that the pods are running correctly.
4. Test your API endpoint.

Let me know if you need help with any specific step!

In depth explain

### Understanding the Commands Step-by-Step

Let's break down each command and its purpose, along with some fundamental concepts.

---

### 1. **Docker**

#### What is Docker?

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow developers to package up an application with all the parts it needs, such as libraries and dependencies, and ship it as one package.

#### Docker Commands:

- **Build the Docker Image:**

  ```bash
  docker buildx build --platform linux/amd64 -t soboards/billboardserver:latest .
  ```

  - **Explanation:** This command builds a Docker image for your application using the `Dockerfile` in the current directory. The `-t soboards/billboardserver:latest` part tags the image with a name (`soboards/billboardserver`) and a version (`latest`). The `--platform linux/amd64` ensures compatibility with most deployment environments.

- **Push the Docker Image:**
  ```bash
  docker push soboards/billboardserver:latest
  ```
  - **Explanation:** This command uploads your Docker image to Docker Hub (or another container registry). This allows you to easily deploy this image to other environments, like Kubernetes.

---

### 2. **Kubernetes (K8s)**

#### What is Kubernetes?

Kubernetes is an open-source platform designed to automate the deployment, scaling, and operation of containerized applications. It helps manage multiple containers across multiple machines in a cluster.

#### Kubernetes Commands:

- **Apply Kubernetes Configurations:**

  ```bash
  kubectl apply -f your-ingress.yaml
  kubectl apply -f deployment.yaml -n soboards
  kubectl apply -f /Users/sahilprasad/Desktop/devalicious/soboards/billboardserver/k8s/service.yaml -n soboards
  ```

  - **Explanation:** These commands deploy resources defined in YAML configuration files (`your-ingress.yaml`, `deployment.yaml`, `service.yaml`) to your Kubernetes cluster. The `-n soboards` specifies the namespace where these resources should be created.

  - **Resources Explained:**
    - **Deployment:** Describes the desired state for your application (e.g., how many replicas/pods you want running).
    - **Service:** Defines how to access your application (internally or externally).
    - **Ingress:** Manages external access to the services, typically HTTP/HTTPS routes.

- **View Ingress and Pods:**
  ```bash
  kubectl get ingress -n soboards
  kubectl get pods -n soboards
  ```
  - **Explanation:** These commands check the status of the Ingress resource and the running Pods in the `soboards` namespace. Pods are the smallest deployable units in Kubernetes, each representing one or more containers.

---

### 3. **AWS EKS (Elastic Kubernetes Service)**

The deployment process takes your backend service, wraps it in a Docker container, and runs it on a cloud infrastructure (AWS EKS).

#### What is AWS EKS?

AWS EKS is a managed Kubernetes service that makes it easy to run Kubernetes on AWS without needing to install and operate your own Kubernetes control plane or nodes.

#### EKS Commands:

- **Create EKS Cluster:**

  ```bash
  eksctl create cluster --name soboards-cluster --region ap-south-1 --fargate
  ```

  - **Explanation:** This command creates an EKS cluster named `soboards-cluster` in the `ap-south-1` region (Mumbai). It uses AWS Fargate, which is a serverless compute engine for containers, meaning you don't have to manage the underlying EC2 instances.

- **Update Kubeconfig:**

  ```bash
  aws eks update-kubeconfig --name soboards-cluster --region ap-south-1
  ```

  - **Explanation:** This command updates the kubeconfig file on your machine, allowing you to interact with your new EKS cluster using `kubectl`.

- **Create Fargate Profile:**

  ```bash
  eksctl create fargateprofile --cluster soboards-cluster --region ap-south-1 --name alb-deploy-app --namespace soboards
  ```

  - **Explanation:** This command creates a Fargate profile, which allows you to specify which Pods should use Fargate for deployment. In this case, it’s for the `soboards` namespace.

- **Create Namespace:**
  ```bash
  kubectl create namespace soboards
  ```
  - **Explanation:** Namespaces are a way to organize resources in a Kubernetes cluster. This command creates a namespace called `soboards`.

---

### 4. **AWS ACM (Certificate Manager)**

#### What is AWS ACM?

AWS Certificate Manager (ACM) is a service that lets you easily provision, manage, and deploy SSL/TLS certificates for use with AWS services and your internal connected resources.

#### ACM Command:

- **Import Certificate:**
  ```bash
  aws acm import-certificate --certificate fileb://domain.cert.pem --private-key fileb://private.key.pem --certificate-chain fileb://intermediate.cert.pem
  ```
  - **Explanation:** This command imports an existing SSL certificate (along with its private key and certificate chain) into AWS ACM. This is useful for securing your application with HTTPS.

---

### Summary

1. **Docker:** You built and pushed a Docker image of your backend application to Docker Hub.
2. **Kubernetes:** You deployed this Docker image to your Kubernetes cluster, configuring services, deployments, and an ingress for external access.
3. **AWS EKS:** You set up an AWS EKS cluster using Fargate, configured it, and deployed your Kubernetes resources there.
4. **AWS ACM:** You imported an SSL certificate to use HTTPS for secure communication.

These steps are crucial in setting up a scalable, secure, and manageable environment for your application, leveraging cloud-native technologies.

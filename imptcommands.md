# commands used for backend deployment

## AWS Configuration

aws configure

## ACM Certificate Import

```bash

aws acm import-certificate --certificate fileb://domain.cert.pem
--private-key fileb://private.key.pem
--certificate-chain fileb://intermediate.cert.pem
```

## Kubernetes Deployment and Service

kubectl apply -f your-ingress.yaml
kubectl apply -f deployment.yaml -n soboards
kubectl apply -f /Users/sahilprasad/Desktop/devalicious/soboards/billboardserver/k8s/service.yaml -n soboards

## Docker Build and Push

docker buildx build --platform linux/amd64 -t soboards/billboardserver:latest .
docker push soboards/billboardserver:latest

## EKS Cluster Creation

eksctl create cluster --name soboards-cluster --region ap-south-1 --fargate
aws eks update-kubeconfig --name soboards-cluster --region ap-south-1
eksctl create fargateprofile --cluster soboards-cluster --region ap-south-1 --name alb-deploy-app --namespace soboards
kubectl create namespace soboards

## Additional Commands

kubectl get ingress -n soboards
kubectl get pods -n soboards
kubectl get pods -n kube-system
kubectl logs soboards-backend-deployment-799b6b995c-476ql -n soboards
kubectl get service -n soboards
kubectl describe ingress soboards-backend-ingress -n soboards
kubectl get ingressclass

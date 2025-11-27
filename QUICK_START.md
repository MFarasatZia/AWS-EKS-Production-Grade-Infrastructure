# 🚀 Quick Start Guide

**Get up and running in 10 minutes!**

---

## Step 1: Prerequisites (5 min)

```bash
# Install required tools
brew install kubectl helm aws-cli

# Configure AWS credentials
aws configure

# Set environment variables
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
export CLUSTER_NAME=demo-eks-cluster
export AWS_REGION=us-east-1

# Verify cluster connection
kubectl get nodes
```

---

## Step 2: Deploy Base Infrastructure (2 min)

```bash
# Apply namespaces with security configurations
kubectl apply -f manifest/namespace-*.yaml

# Apply storage class
kubectl apply -f manifest/storageclass-gp3.yaml

# Verify
kubectl get namespaces
kubectl get storageclass
```

---

## Step 3: Deploy Autoscaling (2 min)

```bash
# Deploy Karpenter configuration
kubectl apply -f karpenter/provisioner.yaml

# Deploy KEDA scaling
kubectl apply -f keda/sqs-scaleobject.yaml

# Verify
kubectl get nodepool -n karpenter
kubectl get scaledobject -A
```

---

## Step 4: Deploy Monitoring (1 min)

```bash
# Deploy KRR for resource optimization
kubectl apply -f krr/krr-*.yaml

# Deploy SonarQube for code quality
kubectl apply -f sonarqube/*.yaml

# Check status
kubectl get pods -n tools
kubectl get pods -n dev -l app=sonarqube
```

---

## 📊 Quick Commands Reference

```bash
# 🔍 Check everything running
kubectl get all -A

# 📈 Monitor autoscaling
kubectl get hpa -A -w

# 📍 Check Karpenter nodes
kubectl get nodes -L karpenter.sh/provisioner-name

# 🔑 Verify IRSA setup
kubectl describe sa app-service-account -n mylera

# 📊 Access KRR Dashboard
kubectl port-forward -n tools svc/krr-dashboard 8080:8080
# Open: http://localhost:8080

# 🎯 Access SonarQube
kubectl port-forward -n dev svc/sonarqube 9000:9000
# Open: http://localhost:9000

# 📝 View logs
kubectl logs -f deployment/node-mylera -n mylera

# 🛠️ Check resource usage
kubectl top nodes
kubectl top pods -n mylera

# ✅ Verify scaling in action
kubectl describe scaledobject backend-sqs-scaler -n dev
```

---

## 🔒 Security Check

```bash
# Verify no pods running as root
kubectl get pods -A -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\t"}{.spec.containers[*].securityContext.runAsUser}{"\n"}{end}'

# Check network policies
kubectl get networkpolicies -A

# Verify resource quotas
kubectl describe resourcequota -A

# Check IRSA annotations
kubectl describe sa app-service-account -n mylera
```

---

## 💰 Cost Optimization Check

```bash
# View Karpenter-provisioned nodes
kubectl get nodes -L karpenter.sh/provisioner-name,node.kubernetes.io/instance-type

# Check pod resource requests
kubectl get pods -A -o custom-columns=NAME:.metadata.name,NS:.metadata.namespace,CPU:.spec.containers[*].resources.requests.cpu,MEM:.spec.containers[*].resources.requests.memory

# Monitor KRR recommendations
kubectl port-forward -n tools svc/krr-dashboard 8080:8080
# Dashboard shows cost-saving recommendations
```

---

## 🚨 Troubleshooting Quick Fixes

### Pods stuck in Pending?
```bash
kubectl describe pod <pod-name> -n <namespace>
# Check: Node resources, networking, quotas, limits
```

### KEDA not scaling?
```bash
kubectl logs -n keda -l app.kubernetes.io/name=keda
kubectl describe scaledobject <name> -n <namespace>
```

### Karpenter not provisioning nodes?
```bash
kubectl logs -n karpenter -l app.kubernetes.io/name=karpenter
kubectl describe nodepool default -n karpenter
```

---

## 📚 Deep Dive Documentation

- **Full Guide**: See `README.md` (1,119 lines - comprehensive)
- **Enhancements**: See `ENHANCEMENTS.md` (detailed changelog)
- **Architecture**: Check `README.md` > Architecture section
- **Troubleshooting**: Check `README.md` > Troubleshooting section

---

## ✅ What You Get

✅ **Automatic scaling**: Pod + Node level (KEDA + Karpenter)
✅ **Cost savings**: 60-70% reduction (Spot instances + right-sizing)
✅ **Security**: IRSA, network policies, encrypted storage
✅ **Observability**: SonarQube, KRR, Prometheus-ready
✅ **Production-ready**: All best practices implemented

---

**Need help?** Check `README.md` → Troubleshooting section
**Want details?** Check `ENHANCEMENTS.md` → Component Guides

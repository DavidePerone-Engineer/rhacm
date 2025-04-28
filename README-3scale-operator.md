# 3scale GitOps Deployment operator with Red Hat Advanced Cluster Management (RHACM)

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) [![GitOps Ready](https://img.shields.io/badge/GitOps-Ready-success.svg)](https://www.gitops.tech/) 


## Overview
This repository implements GitOps practices for deploying 3scale API Management on OpenShift clusters using RHACM policies. The structure follows Kubernetes operator lifecycle management patterns with Kustomize-based overlays.

## Prerequisites
- Red Hat Advanced Cluster Management 2.6+
- OpenShift 4.10+ target clusters
- GitLab repository configured
- RHACM hub cluster administrator access
- ManagedClusterSet resource permissions
- Namespace binding permissions for cluster sets



## Required Resources created on RHACM to be able to use the policy generated

1. Namespace created  to place policies on this case acm-policies


2. Create a ManagedClusterSet with button create cluster set

![image](https://github.com/user-attachments/assets/3112455f-9850-4a7b-96ae-38208c5d866a)

3. bind namespace acm-policies  on ManagedClusterSet created

![image](https://github.com/user-attachments/assets/72d3bc91-5165-408e-8f30-ecb77018def4)

![image](https://github.com/user-attachments/assets/34a4964d-484f-4772-83f0-111d242a48ce)


4. add your clusters on previous cluster set created  ( the same cluster can't be added to other clustersets)

   ![image](https://github.com/user-attachments/assets/9304e338-376a-42d6-b66a-d0b80ada6580)

   ![image](https://github.com/user-attachments/assets/2b2f2e2c-a81f-4be8-98e2-7191a38199a7)



4. Required cluster-role-binding

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: open-cluster-management:subscription-admin-0
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: open-cluster-management:subscription-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: system:admin
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: kube:admin
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: admin
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: cluster-admins
```


  #Rhacm placement example (check always the api version related to your rhacm version , the placement must be created on namespace previously binded acm-policies)
```
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
   name: rhacm-placement
   namespace: acm-policies
spec:
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            local-cluster: "true"
 ```           
#Managed Cluster placement example

	apiVersion: cluster.open-cluster-management.io/v1beta1
	kind: Placement
	metadata:
	   name: dap-ipi01-placement
	  namespace: acm-policies
	spec:
	  predicates:
	    - requiredClusterSelector:
	        labelSelector:
	          matchExpressions:
	            - key: name
	              operator: In
	              values:
                - dap-ipi01

## attach this repository on rhacm

![image](https://github.com/user-attachments/assets/889258cb-e029-4178-9d69-4ca68191508a)

![image](https://github.com/user-attachments/assets/92f92aeb-0e00-49c2-b617-e44a1adff04d)

![image](https://github.com/user-attachments/assets/ca8c6803-1856-49b4-aab2-af14f104d634)

![image](https://github.com/user-attachments/assets/a5107cbd-4b65-4b5e-b920-a91fd5c3ba5e)

![image](https://github.com/user-attachments/assets/c67e51e0-e94a-4adc-878b-f1ce890528b3)





# Layout
  ## applications # A folder in the same repo for simplicity, but ideally a repo per app
    - app1
    - app2

  ## charts # All Charts go here
    - All Charts 

  ## Clusters # Individually deployed clusters with reference to deployments and apps
    - cluster1
    - Cluster2
    - Cluster3

  ## Deployments # deployment specific for different types of cluster builds i.e. sno vs hci vs vm
    - deployment1
    - deployment2
    - deployment3


# Deploy Argo
##
```yaml
apiVersion: v1
kind: Namespace
metadata:
  labels:
    openshift.io/cluster-monitoring: "true"
    pod-security.kubernetes.io/enforce: privileged
    pod-security.kubernetes.io/audit: privileged
    pod-security.kubernetes.io/warn: privileged
  name: openshift-gitops-operator
```

## Create Role Binding for Argo svc Account cluster-admin
```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: openshift-gitops-argocd-application-controller-clusteradmin
subjects:
  - kind: ServiceAccount
    name: openshift-gitops-argocd-application-controller
    namespace: openshift-gitops
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
```

# OpenShift GitOps Operator Group
```yaml
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: openshift-gitops-operator-
  name: openshift-gitops-operator-cluster
  namespace: openshift-gitops-operator
spec:
  upgradeStrategy: Default
```

# OpenShift GitOps Subscription
```yaml
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  labels:
    operators.coreos.com/openshift-gitops-operator.openshift-gitops-operator: ''
  name: openshift-gitops-operator
  namespace: openshift-gitops-operator
spec:
  channel: latest
  installPlanApproval: Automatic
  name: openshift-gitops-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
  startingCSV: openshift-gitops-operator.v1.12.1
```

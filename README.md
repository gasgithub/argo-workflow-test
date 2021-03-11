# argo-workflow-test

## Argo setup issues
Quickstart fails to create postgeres - used provided by RH

When starting sample workflow:
Invalid value: "hostPath": hostPath volumes are not allowed to be used

https://github.com/argoproj/argo-workflows/issues/1272


https://github.com/argoproj/argo-workflows/issues/2581
failed to save outputs: unexpected non 200 status code: 403, body: Forbidden (user=system:serviceaccount:argo:default, verb=get, resource=nodes, subresource=proxy


oc describe role/argo-role
Name:         argo-role
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"rbac.authorization.k8s.io/v1","kind":"Role","metadata":{"annotations":{},"name":"argo-role","namespace":"argo"},"rules":[{"...
PolicyRule:
  Resources                                 Non-Resource URLs  Resource Names  Verbs
  ---------                                 -----------------  --------------  -----
  persistentvolumeclaims                    []                 []              [create delete get]
  poddisruptionbudgets.policy               []                 []              [create get delete]
  pods/exec                                 []                 []              [create get list watch update patch delete]
  pods                                      []                 []              [create get list watch update patch delete]
  events                                    []                 []              [create patch]
  workflows.argoproj.io/finalizers          []                 []              [get list watch update patch delete create]
  workflows.argoproj.io                     []                 []              [get list watch update patch delete create]
  cronworkflows.argoproj.io/finalizers      []                 []              [get list watch update patch delete]
  cronworkflows.argoproj.io                 []                 []              [get list watch update patch delete]
  workflowtemplates.argoproj.io/finalizers  []                 []              [get list watch]
  workflowtemplates.argoproj.io             []                 []              [get list watch]
  serviceaccounts                           []                 []              [get list]
  configmaps                                []                 []              [get watch list]
  secrets                                   []                 []              [get]
  
  
  oc create clusterrolebinding argo-admin --clusterrole=admin --serviceaccount=argo:argo
  
  oc create clusterrolebinding argo-executor --clusterrole=argo-executor --serviceaccount=argo:default
  
  
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: argo-executor
rules:
# pod get/watch is used to identify the container IDs of the current pod
# pod patch is used to annotate the step's outputs back to controller (e.g. artifact location)
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - get
  - watch
  - patch
# logs get/watch are used to get the pods logs for script outputs, and for log archival
- apiGroups:
  - ""
  resources:
  - pods/log
  - nodes/proxy
  verbs:
  - get
  - watch

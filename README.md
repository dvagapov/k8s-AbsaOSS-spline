# k8s-AbsaOSS-spline

## set up ENVs
export ARANGO_VERSION=1.2.7

export URLPREFIX=https://github.com/arangodb/kube-arangodb/releases/download/$ARANGO_VERSION

NAMESPACE=spline

### deploy arangodb-operator

helm install kube-arangodb-crd $URLPREFIX/kube-arangodb-crd-$ARANGO_VERSION.tgz --create-namespace -n $NAMESPACE

helm install kube-arangodb $URLPREFIX/kube-arangodb-$ARANGO_VERSION.tgz -n $NAMESPACE

### check deployments
kubectl get all -n $NAMESPACE

### deploy arangodb-cluster
helm upgrade --install arangodb charts/arangodb --set auth.password=MySuperStrongPassword -n $NAMESPACE

### deploy Spline and Spline-UI
helm install spline charts/spline --set ingress.host="spline-api.example.com" --set arango.url="arangodb://root:MySuperStrongPassword@arangodb:8529" --set examples.enabled=true -n $NAMESPACE

helm install spline-ui charts/spline-ui --set ingress.host="spline-api.example.com" --set splineConsumerUrl="https://spline:8080/consumer" -n $NAMESPACE
instructions from

https://learn.openshift.com/ansibleop/ansible-operator-overview/

Prerequisites
Docker
oc client
Install operator-sdk - https://github.com/operator-framework/operator-sdk/blob/master/README.md

Generated project using

```sh
operator-sdk new memcached-operator --type=ansible --api-version=cache.example.com/v1alpha1 --kind=Memcached --skip-git-init
```

replaced generated role with existing role in ansible galaxy

```sh
ansible-galaxy install dymurray.memcached_operator_role 
```

The task in this role has the k8s definition of the Deployment. The watches.yaml file needs to be updated to watch this role


## create CRD
```sh
oc create -f deploy/crds/cache_v1alpha1_memcached_crd.yaml --as system:admin
```

## build operator image
```sh
operator-sdk build memcached-operator:v0.0.1
```

The image will be pushed to the local registry and will need to be pushed to an external registry. For e.g
```sh
docker push quay.io/example/memcached-operator:v0.0.1
```

Update deploy/operator.yaml with the appropriate image and imagePullPolicy

## Add the service account, role and role binding

```sh
oc create -f deploy/service_account.yaml
oc create -f deploy/role.yaml --as system:admin
oc create -f deploy/role_binding.yaml --as system:admin
```

## Deploy the operator 
```sh
oc create -f deploy/operator.yaml
```

## verify that the operator is deployed
```sh
oc get deployment memcached-operator
```

## create a memcached cr instance, feel free to change the spec fields on the CR
```sh
oc create -f deploy/crds/cache_v1alpha1_memcached_cr.yaml 
```

## verify
```sh
oc get deployment 
```

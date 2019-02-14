# Custom-Role-for-OCP

I created a custom role from the cluster-reader role to accomodate a customer request for a role that would have all the priveledges of cluster-admin without being able to create/delete/view roles or rolebindings

`oc get clusterrole cluster-reader -o yaml > cluster-admin-norbac.yaml`

`vim cluster-admin-norbac.yaml`

I modified all the verbs in the new yaml that had the following stanza  

```
  verbs:
  - get
  - list
  - watch
```
to
```
  verbs:
  - create
  - delete
  - deletecollection
  - get
  - list
  - patch
  - update
  - watch
```
I also commented out 3 sections within that file just for future reference.  See sections with #
I then logged in with cluster-admin credentials

```
oc login -u admin <cluster>
```
added the new cluster policy to the user ( but this could also be a group using the `add-cluster-role-to-group` )
```
oc adm policy add-cluster-role-to-user cluster-admin-norbac matt
cluster role "cluster-admin-norbac" added: "matt"
```
prove it works
```
[matt@openshift-master3 ~]# oc login -u matt
Authentication required for https://console.example.com:8443 (openshift)
Username: matt
Password: 
Login successful.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    cicd
  * default
    development
    etcd-test
    heptio-ark
    java-build
    kube-public
    kube-system
    management-infra
    matt
    openshift
    openshift-console
    openshift-infra
    openshift-logging
    openshift-metrics-server
    openshift-monitoring
    openshift-node
    openshift-sdn
    openshift-web-console
    production
    test
    test2
    testing

Using project "default".
[matt@openshift-master3 ~]# oc get role
No resources found.
Error from server (Forbidden): roles.authorization.openshift.io is forbidden: User "matt" cannot list roles.authorization.openshift.io in the namespace "default": no RBAC policy matched


[matt@openshift-master3 ~]# oc get rolebinding
No resources found.
Error from server (Forbidden): rolebindings.authorization.openshift.io is forbidden: User "matt" cannot list rolebindings.authorization.openshift.io in the namespace "default": no RBAC policy matched


[matt@openshift-master3 ~]# oc delete project matt
project.project.openshift.io "matt" deleted
```


# grafana-custom
Custom Grafana for OpenShift 3.11

By default OpenShift 3.11 Grafana is a read-only instance. Many organizations may want to add new custom dashboards. This custom grafana will interact with existing Prometheus and will also add all out-of-the-box dashboards plus few more interesting dashboards which may require from day to day operation. Custom Grafana pod uses OpenShift oAuth to authenticate users and assigns "Admin" role to all users so that users can create their own dashboards for additional monitoring.

## To deploy it manually under openshift-monitoring project alongside existing Grafana

* Clone the repository and switch into the directory. Log in as a cluster-admin user and switch to openshift-monitroing project:
```
cd grafana-custom
oc project openshift-monitoring
```

* Create a Service Account named grafana-custom:
```
oc apply -f grafana-sa.yml
```

* Assign existing "grafana" role to grafana-custom Service Account:
```
oc apply -f grafana-clusterrolebinding.yml
```

* Create a secret for grafana-custom configuration:
```
oc apply -f grafana-custom-config-secret.yml
```

* Create few custom dashboards. All existing OpenShift dashboards will be imported automatically:
```
oc apply -f dashboard-capacity.yml
oc apply -f dashboard-master-api.yml
oc apply -f dashboard-pods.yml
oc apply -f dashboard-traffic.yml
```

* Create a service for grafana-custom:
```
oc apply -f grafana-service.yml
```

* Create a route for the grafana-custom:
```
oc apply -f grafana-route.yml
```

* Finally, create the grafana-custom Pod:
```
oc apply -f grafana-deployment.yml
```

## To deploy it using openshift-applier

An [OpenShift Applier](https://github.com/redhat-cop/openshift-applier) inventory has been supplied for deployment convenience. Two playbooks are also provided. The first is to deploy the OpenShift Monitoring stack, using [openshift-ansible](https://github.com/openshift/openshift-ansible), the second deploys the custom Grafana.

Run the following to deploy.

```
ansible-galaxy install -r requirements.yml -p galaxy
ansible-playbook -i /path/to/cluster/inventory/ monitoring.yml
ansible-playbook -i .applier/ apply.yml
```

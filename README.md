# Change-Host-Subnet-on-Openshift
This steps describes about how to change host subnet sizing on Openshift Cluster. 
```
Changing Hostsubnet

1) take snapshot of vm
2)   oc get nodes                      -o yaml    >> oc_get_node.out           
     oc get pods      --all-namespaces -o yaml    >> oc_get_pods.out
     oc get services  --all-namespaces -o yaml    >> oc_get_svc.out           
     oc get endpoints --all-namespaces -o yaml    >> oc_get_ep.out          
     oc get routes    --all-namespaces -o yaml    >> oc_get_rte.out          
     oc get clusternetwork             -o yaml    >> oc_get_CN.out           
     oc get hostsubnets                -o yaml    >> oc_get_hs.out          
     oc get netnamespaces              -o yaml    >> oc_get_ns.out           

	 
1- Evacuate every node in the OpenShift Container Platform cluster:
       > for i in $(oc get node -o name); do oc adm drain ${i}; done
2- Ensure that there aren't any pods in the cluster. Do not continue if there are pods even if they are in Terminating state
       > oc get pod --all-namespaces
3- Stop the service in every OpenShift Container Platform - Node, including masters:
       > systemctl stop atomic-openshift-node
4- Delete every hostsubnet
       > oc delete hostsubnet --all
5- In /etc/origin/master/master-config.yaml modify on every OpenShift Container Platform - Master:

	Raw
	networkConfig:
  		clusterNetworkCIDR: 10.128.0.0/20
		hostSubnetLength: 8

6- Delete the default clusterNetwork
	oc delete clusterNetwork default
7- In every master restart all the master services:
	systemctl restart atomic-openshift-master-api systemctl restart atomic-openshift-master-controllers
8- Verify the clusternetwork default is properly created (this may take a few minutes):
	oc get clusternetwrok
9- Reboot the OpenShift Container Platform - Nodes
10- Reboot the OpenShift Container Platform - Masters
11- Verify the hostsubnets are in the new range running
	oc get hostsubnet
12- Modify the ansible inventory to match the current configuration
13- Run the installation playbook so that everything is consistent. This step is particularly important when using a proxy, to properly set no_proxy settings.

```

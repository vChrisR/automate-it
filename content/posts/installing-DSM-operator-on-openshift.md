+++
title = "Installing DSM Operator on Openshift"
date = 2024-09-12T16:00:59+02:00
images = []
tags = []
categories = []
draft = false
+++
I recently had to deploy the VMware DSM Consumption operator on an openshift cluster. Reading the docs it seemed easy enough. Just install a helm chart and off we go.... of course it took a bit more than that so I decided to document it here in case I or anyone else would later need it.

## What is it?
Let me introduce the DSM consumption operator first: It is a kubernetes operator that enables you to deploy databases in VMwares DBaaS platform DSM (Data services manager) from the "comfort" of a kubernetes yaml file.

## So how do I install it?
This [doc](https://docs.vmware.com/en/VMware-Data-Services-Manager/2.0/data-services-manager/GUID-cfg_dsm_consumption_operator.html) explains how to deploy it to kubernetes. However there are a few problems with this doc in its current form (I'm writing this on 09-12-2024, the doc is last updated on 05-24-2024):
- The doc suggests installing version 1.0.0 which didn't work for me at all. I used version 1.1.2 which actually did work. So when pulling the helm chart use this command instead of the one from the doc: ``helm pull oci://projects.packages.broadcom.com/dsm-consumption-operator/dsm-consumption-operator --version 1.1.2 -d consumption/ --untar``
- When creating the helm values.yml files in step 2 of the install procedure make sure to change the image.tag to the same version as the helm chart. So 1.1.2 in this case. Stick to the docs for the rest of the config.
- The doc suggest creating a pull secret with a username/password if ignore/ignore. This didn't work. On request VMware actually removed the authentication from their registry. So now you can run the operator without the pull secret. Unfortunately helm still puts the secret name in the deployment yaml. So I rendered out the yaml files and made a manual edit....

Rendering out the yamls is very similar to running helm install. Here is how you do it: ``helm template dsm-consumption-operator consumption/dsm-consumption-operator, -f ./values-override.yml --include-crds --namespace dsm-consumption-operator-system > dsm-operator.yml``

- Now edit the generated yaml file. Look for the "Deployment" kind and delete the "imagePullSecret" attribute.

- Of you're not on openshift you should be good to just kubectl apply the dsm-operator.yml file now. If you're on openshift, keep reading.

## The openshift trick
Openshift assigns a range of UIDs en GUIDs to each namespace (or should I say project?). However, the operator fires of a few jobs to configure its webhook which want to run as user 2000. Which is well outside the range of what openshift wnat to see. 

So we need to tell openshift this is alright. We do that by running this command: ``oc adm policy add-scc-to-user privileged -z dsm-consumption-operator-controller-manager``. Make sure your context points to the dsm-consumption-operator-system namespace (pro tip: use [ns and ctx plugins](https://github.com/ahmetb/kubectx)). Since this permisison only seems to be used for the jobs that run right after installation I believe you can remove the permission after the 3 jobs are completed (``oc adm policy remove-scc-from-user privileged -z dsm-consumption-operator-controller-manager``)


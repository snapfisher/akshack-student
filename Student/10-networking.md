# Challenge 10: DNS & Ingress

[< Previous Challenge](./09-helm.md) - **[Home](../README.md)** - [Next Challenge >](./11-privateaks.md)

## Introduction

We started out with some very simple, default networking that Kubernetes gives us for free. But this is rarely what we'll need to go into production. Now we'll get a little more in depth on networking in Kubernetes, and investigate DNS as well as Ingress Controllers.

## Description

In this challenge you will be enabling DNS, installing an Ingress Controller and learning how the "Ingress" resource in Kubernetes works. 

Note:  This challenge will be a bit more "guided" than the others.   While these features are straightforward to implement (as you will see), it could be quite challenging (no pun intended) to get them working in a short period of time with just a "read the docs" approach.

## Part 1:  DNS for Public IPs
In the previous challenges, we accessed our service via an IP address.  What if you want to use a DNS name to reach the service?  That's possible using the following method:

1. You need to add a metadata annotation to your content-web service.  See [this link](https://docs.microsoft.com/en-us/azure/aks/static-ip#apply-a-dns-label-to-the-service) for more information.
2. Your service should now be available at the url http://[myserviceuniquelabel].[location].cloudapp.azure.com.   
3. Verify that the DNS record has been created (nslookup or dig), and then test this url in your browser.
4. Discuss with your coach how you might link a 'real' DNS name (eg, conferenceinfo.fabmedical.com) with this "azure-specific" DNS name (eg, conferenceinfo.eastus.cloudapp.azure.com)

## Part 2a: Ingress Controller
Switching gears, we will now start working with ingress controllers, which allow you to route http requests.

1. Delete the existing content-web service.
2. You need to install the nginx ingress controller using helm. See https://docs.microsoft.com/en-us/azure/aks/ingress-basic for instructions.
3. Deploy the content-web service and create an Ingress resource for it. 
	- The reference template (which will need editing!) can be found in the Challenge 10 Resources folder: `template-web-ingress-deploy.yaml`

## Part 2b: Ingress Controller + DNS for Public IPs
Just like in part 1, you will now add a metadata annotation to the ingress controller to configure a dns name.

1. Adding a dns label to the ingress controller via helm can be tricky.  It's documented at this link: https://docs.microsoft.com/en-us/azure/aks/ingress-static-ip
   - The instructions talk about installing a new ingress controller.  You already have one (from step 2a) so you don't need to do a fresh install.
   - Ignore the notes about creating and using a static IP.  For this challenge, a dynamic IP is fine.
   - Specifically, you will need to modify (upgrade) your previously-installed ingress controller deployment as follows:  _Note: This example is formatted for bash.  For powershell, change the line continuation character from \ to `_
```bash
helm upgrade  nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic --reuse-values \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="NEW-DNS-LABEL"
```
2. Now that the ingress controller has been updated, you need to update the ingress yaml you created earlier  in part 2a by uncommenting the `Host:` line and adding in the [new-dns-label].[REGION].cloudapp.azure.com you chose.
2. Verify that the DNS record has been created (nslookup or dig), and then access the application using the DNS name, e.g: 
    - `http://[new-dns-label].[REGION].cloudapp.azure.com`


## Success Criteria

1. The nginx Ingress Controller is installed in your cluster
1. You've recreated a new Ingress for content-web that allows access through a domain name.


---
description: Security considerations for DevOps teams and Ministry information security teams
resourceType: Documentation
title: BC Government OpenShift DevOps Security Considerations
tags:
  - security
  - privacy
  - secrets
  - tls
  - ssl
  - pipeline
  - templates
  - container scanning
  - STRA
  - network policies
  - vault
---

# BC Government OpenShift DevOps Security Considerations

## DevOps Security Tools and Capabilities

### Summary

There are a number of tools available to developers working on the OpenShift platform to help ensure the confidentiality, integrity and availability of data within those systems.  This is an overview of those tools, with links to specifics on each of the resources.

- [OpenShift Platforrm Security](#openshift-platform-security)
- [Privacy](#privacy)
- [Critical Systems Standard](#critical-systems-standard)
- [Platform Tools Security Assessments](#platform-tools-security-assessments)
- [Platform Product Registry](#platform-product-registry)
- [Communications](#communications)
- [Access Management](#access-management)
- [Kubernetes Network Policies](#kubernetes-network-policies)
- [Pipeline Templates (includes static and dynamic analysis)](#pipeline-templates)
- [Container image scanning (Aqua, Xray)](#container-image-scanning)
- [Container runtime security](#container-runtime-security)
- [TLS Certificates](#tls-certificates)
- [Secrets Management](#secrets-management)
- [GitOps/Cluster Configuration Management](#gitops-cluster-configuration-management)
- [API Management](#api-management)
- [Application Resource Tuning Advisor and App Assessment Tool](#application-resource-tuning)
- [Logging/Monitoring (EKS, Kibana, Graphana, Sysdig Monitor, SIEM)](#logging-monitoring)
- [Backups](#backups)
- [Change Management](#change-management)
- [GitHub](#github)
- [Other considerations](#other-considerations)

## Tools & Capabilities
### <a name="openshift-platform-security"></a>OpenShift Platform Security

Service definition - Silver/Gold - https://cloud.gov.bc.ca/private-cloud/our-services-in-private-cloud-paas/get-started-with-the-private-cloud-paas/

Our **_Silver Service_** is our standard DevOps platform offering, with on-cluster resiliencey based on application design.

Our **_Gold Service_** is our enhanced DevOps platform offering, with replication to a secondary cluster for disaster fail-over purposes.  

Please take note of the [**_Shared Responsibility Model_**](https://cloud.gov.bc.ca/private-cloud/your-product-team-in-the-private-cloud-paas/our-shared-responsibilities/).  While the Platform Services Team manages infrastructure, OpenShift Container Platform and the Platform critical services as part of the Private Cloud PaaS, the Product Team bears the responsibility for the functionality and operations of their application(s) hosted on the Platform.  

Specific details on OpenShift specific secuirty controls can be found here:

https://www.redhat.com/rhdc/managed-files/cl-openshift-security-guide-ebook-us287757-202103.pdf

- these are highlighted as part of the OpenShift STRA.

**_Penetration Tests_**

The platform services team outsources for a penetration test annually to ensure the services we provide are configured to protect confidentiality, integrity and availability.  Peneteration tests are procured through the pre-qualified list of vendors (https://www2.gov.bc.ca/gov/content/governments/services-for-government/bc-bid-resources/goods-and-services-catalogue/im-it-security-services).

------
### <a name="privacy"></a>Privacy
A Privacy Impact Assessment has been completed for the OpenShift Container Platform service.

Personal Information upto and including Protected B Information Security Classification may be stored on OpenShift.   
- https://www2.gov.bc.ca/gov/content/governments/services-for-government/information-management-technology/information-security/information-security-classification

------
### <a name="critical-systems-standard"></a>Critical Systems Standard

We are very close to obtaining critical systems standard compliance.  
Documentation is in final stages of review for submission.

------
### <a name="platform-tools-security-assessments"></a>Platform Tools Security Assessments

Many of the platform tools have ***completed*** security assessments.  These include:
- OpenShift v4.x and GitHub (Public)
- KeyCloak
- KeyCloak Realm Registry
- KeyCloak Common Host Single Sign-on
- Artifactory
- Sysdig Monitor
- Just Ask!
- Certbot
- Mautic
- Rocket Chat
- Vault
- OCP Application Resource Tuning Advisor
- Uptime.com
- Stack Overflow

- 1Password (SoAR complete, Cloud security schedule review complete) - not to be used corporately due to no background checks for staff/contractors

The following security assessments are ***underway***:
- Platform Product Registry
- GitHub Enterprise (Cloud security schedule review underway)

The following security assessments are ***planned***:
- Cert Manager for OpenShift
- Kyverno
- GitGuardian
- Platform Security Dashboard

For specifics, please contact the platform services team at PlatformServicesTeam@gov.bc.ca.

------
### <a name="platform-product-registry"></a>Platform Product Registry

Here, we maintain a listing of all products with deployments on each OpenShift cluster. 
![platformregistryexample](https://user-images.githubusercontent.com/53879638/144318853-104588bc-0db8-4cd7-8616-13c1c137f199.JPG)

https://registry.developer.gov.bc.ca/public-landing?redirect=/dashboard

While access to the registry is currently limited to the OpenShift Platform Services team (full view) and Product Owners/Technical Leads (limited view), we are working on creating roles for Ministry security staff to consume as well.  Until then, you can contact PlatformServicesTeam@gov.bc.ca for details.

------
### <a name="communications"></a>Communications

Community sharing, alerts and discussions take place on Rocket Chat, which we host as an app on OpenShift.  Authentication via IDIR or GitHub (in BCGov org or invited by an existing member).
- https://cloud.gov.bc.ca/private-cloud/support-and-community/stay-connected/
- https://just-ask.developer.gov.bc.ca/

#### Mautic

Mautic has been implemented to allow for subscription based communications for the DevOps community. 
- https://github.com/bcgov/Mautic-Openshift
- https://github.com/bcgov/Mautic-Subscription-App
- https://subscribe.developer.gov.bc.ca/

------
### <a name="access-management"></a>Access Management

Access to OpenShift is brokered through our OpenShift SSO Service (currently leveraging KeyCloak).

- https://github.com/bcgov/sso-keycloak/wiki/SSO-Onboarding
- https://bcgov.github.io/sso-requests


GitHub has been the primary authentication to date on OpenShift, however we are in the process of introducing IDIR (via Azure AD).  Both of these options require an account with 2FA/MFA enabled.  
GitHub - All clusters  
IDIR - KLAB, Silver (Gold, GoldDR to come in early 2022)

- GitHub 2FA - https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication
- IDIR MFA - https://www2.gov.bc.ca/gov/content/governments/services-for-government/information-management-technology/information-security-mfa

Cluster roles are managed either in private GitHub repositories (in the bcgov-c org) or through direct role bindings within a namespace.
We are investigating third party tools to help improve the user management experience.

Platform Services Roles and Responsibilities can be found here:
- https://cloud.gov.bc.ca/private-cloud/your-product-team-in-the-private-cloud-paas/our-shared-responsibilities/

The Platform Services team maintains an Access Control Policy for all platform tools.  This is kept within the IDIR protected area on the following website:
- https://cloud.gov.bc.ca/private-cloud/idir-protected-content/

------
### <a name="kubernetes-network-policies"></a>Kubernetes Network Policies (KNPs)

Network policies help the platform and project teams to better control communications between components.  While KNPs only apply as INGRESS rules (not egress), they help to improve our overall security posture.  KNPs only apply to on-cluster communications (i.e. between pods in a namespace, or between namespaces).  For off-cluster communications, hosting is investigating a VMWare tool called NSX-T.  NSX-T governs network policies for our Emerald cluster.

Find our more about using KNPs to control network security for an application hosted on the Private Cloud Openshift Platform in [this document](https://beta-docs.developer.gov.bc.ca/openshift-network-policies/). 

More details on KNPs can be found here: https://kubernetes.io/docs/concepts/services-networking/network-policies/

------
### <a name="pipeline-templates"></a>Pipeline Templates (includes static and dynamic analysis)

In order to reduce effort in implementing secure tools into a build pipeline, we have developed pipeline templates that include components for build, aas well as static and dynamic vulnerability scanning.  
- https://github.com/bcgov/Security-pipeline-templates/

Here is a representation of what an application build pipeline should look like:
![PlatformSec drawio](https://user-images.githubusercontent.com/53879638/144318963-fdb5b877-88b8-451d-accf-2d24918c0d62.png)

The pipeline templates above make it easier to include the tools described below:
  - https://beta-docs.developer.gov.bc.ca/reusable-services-list/#sonarqube-in-the-bc-gov-private-cloud-paas
  - https://beta-docs.developer.gov.bc.ca/reusable-services-list/#owasp-zap-security-vulnerability-scanning

**What do each of these types of scanning tools do for me?**

Static Anaysis (i.e. SonarX, CodeQL) 
- identifies coding issues that could lead to compromise, back doors, secrets, etc

Dynamic Anaysis (i.e. OWASP ZAP) 
- testing against a live version of app for injection, Cross-site scripting (XSS), and other common web attacks (https://owasp.org/www-project-top-ten/)

Image Analysis 
- ensures image components are up-to-date and not vulnerable to known exploits (https://cve.mitre.org/, https://nvd.nist.gov/).

------
### <a name="container-image-scanning"></a>Container image scanning (ACS, Xray)

Image scanning/analysis comes in 2 forms - 1 active (RedHat Advanced Cluster Security - ACS), 1 passive (XRay).

**ACS:**

This tool allows us to scan image registries and running containers for image vulnerabilities.  
It allows us to create policies at build, deploy, and runtime.  
It can also help in defining network security policies for your application and visualizing component communications.

Scoped access is granted based on identification as a Product Owner or Technical Lead in the OpenShift Product Registry.  
Developer access can be granted by request.  Requests must include the following:
- Namespaces
- Product Owner approval

Links:
- https://acs.developer.gov.bc.ca
- https://www.redhat.com/en/technologies/cloud-computing/openshift/advanced-cluster-security-kubernetes

**XRay:**

An addon capability to Artifactory, XRay scans images and other artifacts for component vulnerabilities.  Anyone with access to an image or artifact within Artifactory can see the XRay tab as part of the image/artifact details, and see what vulnerable components lie within, and what version will correct that deficiency.
- https://artifacts.developer.gov.bc.ca/ui/login/

------
### <a name="container-runtime-security"></a>Container runtime security

We currently have runtime policies in place for the following using ACS.  These look for things like:
- Cryto-mining
- Integrity monitoring
- https://docs.openshift.com/acs/3.66/operating/manage-security-policies.html

Additionally, OpenShift uses CoreOS and the CRI-O container engine.
- https://docs.openshift.com/container-platform/4.10/architecture/architecture-rhcos.html

------
### <a name="tls-certificates"></a>TLS Certificates

OpenShift uses a wildcard certificate for the majority of cluster communications security.  This should be sufficient for dev and test workloads, but for production workloads, each team is required to obtain a dedicated TLS certificate from the Access & Directory Management Services (ADMS) team.  

***Note:*** by default, the wildcard will be used to protect project workloads.  The Platform Services team worked through the wildcard issuance requriements for use on the OpenShift clusters.  Obtaining a dedicated TLS cert is currently a manual process.  
Details on these processes can be found here: https://ssbc-client.gov.bc.ca/services/SSLCert/documents.htm

**Pre-requisites:**
Generate a .csr for each site:
- https://github.com/BCDevOps/openshift-wiki/blob/master/docs/SSLCerts/GenerateCertificateSigningRequest.md

**Ordering Process:**
-  Business area creates/submits order
-  Ministry Service Desk reviews order, creates iStore Number, sends to EA for Approval
-  EA Approves
-  Order is sent to DXC for fulfillment
-  Once order is fulfilled/shipped by DXC, Ministry Service Desk sends 'Completed Order' notification to business area
-  *Note:* This process may be slightly different using MyServiceCentre.

![TLS_Order](https://user-images.githubusercontent.com/53879638/144319065-af4ae9f9-3d61-41b3-977b-36a600e51b0a.png)

------
### <a name="secrets-management"></a>Secrets Management
**OpenShift Secrets:**

This 'secrets' store should actually only be used for configurations.  Values are encoded as base64 and NOT encrypted.  However, these 'secrets' can only be accessed with a role to a given namespace with permission to access them.  Additionally, the physical etcd volume, where OpenShift secrets are stored, is encrypted.

**Vault:**

The preferred secrets management tool for team use on OpenShift.
- https://cloud.gov.bc.ca/private-cloud/our-products-in-the-private-cloud-paas/vault-secrets-management/
- https://beta-docs.developer.gov.bc.ca/vault-secrets-management-service/
- https://beta-docs.developer.gov.bc.ca/vault-getting-started-guide/

------
### <a name="gitops-cluster-configuration-management"></a>GitOps/Cluster Configuration Management

Argo CD provides a GitOps capability for sync'ing a Git repository with an OpenShift configuration (platform or application).
- https://beta-docs.developer.gov.bc.ca/argo-cd-usage/

We are in the process of testing out the GitOps Operator, based on ArgoCD, that is integrated into OpenShift.  This may replace (partially or completely) our custom ArgoCD implementation.

------
### <a name="api-management"></a>Application Programmable Interface (API) Management

The Data BC team hosts an API Gateway for use by other government clients.  Details can be found here:
- https://developer.gov.bc.ca/API-Gateway-(powered-by-Kong-CE)
- https://developer.gov.bc.ca/BC-Government-API-Guidelines
- https://developer.gov.bc.ca/BC-Government-OpenAPI-Specifications

------
### <a name="application-resource-tuning"></a>Application Resource Tuning Advisor and App Assessment Tool
**Resource Tuning Advisor**
- https://github.com/BCDevOps/resource-tuning-advisor-app 

**App Assessment Tool**
- https://github.com/bcgov/AppAssessment

------
### <a name="logging-monitoring"></a>Logging/Monitoring (EKS, Kibana, Graphana, Sysdig Monitor, SIEM, Uptime, Status)

The Platform Services team provides a number of tools to help ensure our platform and applications are behaving as expected, while allowing us to investigate anomolies.

**OpenShift UI:**

Within the OpenShift interface, project teams can view logs associated with a given pod through the Logs tab.  
![logging_ui](https://user-images.githubusercontent.com/53879638/144319141-d387a02c-6b9e-4330-ab5b-6e2711e39565.JPG)

**Kibana:**

This tool provides a more wholistic view of logs for an application or at the platform level, as well as providing visualization and alerting capability.
- https://kibana-openshift-logging.apps.silver.devops.gov.bc.ca/

**Sysdig Monitor:**

This tool allows our platform admins and platfrom teams to build monitoring dashboards.
- https://cloud.gov.bc.ca/private-cloud/our-products-in-the-private-cloud-paas/monitoring-with-sysdig/
- https://beta-docs.developer.gov.bc.ca/sysdig-monitor-onboarding/
- https://app.sysdigcloud.com/#/login

**Security Information and Event Monitoring (SIEM):**

All cluster logs (system, audit, container) are regularly sent to the Security Operations SIEM environment.  
Retention is set as follows:
- System, Container logs - 2 months
- Audit logs - 13 months

We are currently working on Azure AD integration (via KeyCloak) and user role mapping based on OpenShift namespace access.
This activity has paused for the time being but will be re-started in the new year.

**Uptime.com**

This tools help us to observe platform service availability:
- https://cloud.gov.bc.ca/private-cloud/our-services-in-private-cloud-paas/
- https://status.developer.gov.bc.ca/

------
### <a name="backups"></a>Backups
**OpenShift:**
- https://beta-docs.developer.gov.bc.ca/reusable-services-list/#backup-container
- https://beta-docs.developer.gov.bc.ca/database-backup-best-practices/

**GitHub:**
- https://github.com/bcgov-c/platform-services-docs/blob/main/github-backups.md

------
### <a name="change-management"></a>Change Management

Planning for platform and service changes is documented on the Platform Services ZenHub board.  
- https://app.zenhub.com/workspaces/platform-experience-5bb7c5ab4b5806bc2beb9d15/board?repos=220104031  
Any service change will be communicated via the #devops-alerts RocketChat channel.

Strategic level changes are communicated to the DevOps community at regular Community Meetups, as well as to executive groups across government.

------
### <a name="github"></a>GitHub

GitHub is the primary git repository for platform application code.  There are some exceptions that use privately hosted GitLab or other source code repositories.  

Here is a summary of the GitHub organizations we own and their purposes:
- bcgov - main developer git repository for platform application code and/or public sharing.
- bcgov-c - main private git repository used for cluster configuration management and non-public projects.
- bcdevops - alternate git repository for platform application code.  Membership required for access to OpenShift.
- bcgov-platform-services - git repository for platform services team

These resources are available:
- https://beta-docs.developer.gov.bc.ca/bc-government-organizations-in-github/

**GitHub Apps**

Teams may request GitHub apps to be associated with their own or all projects in a GitHub organization.  These requests are reviewed by a platform administrator for validity and scope.  These are also shared with Ministry security staff to ensure they are acceptable for connection.

**GitHub Enterprise**

We are currently piloting the use of GitHub Enterprise.  
- https://beta-docs.developer.gov.bc.ca/github-enterprise-user-licenses-bc-government/
- https://github.com/enterprises/bcgov-ent/sso

------
### <a name="other-considerations"></a><u>Other considerations</u>
**Payment Card Industry Compliance (PCI-DSS)**

Our OpenShift implementation is **NOT** PCI-DSS compliant.  If you wish to host an application on OpenShift that needs to perform financial transactions, please refer to the following:  https://github.com/BCDevOps/developer-experience/blob/37b93d7cbeaa997e31ad320c345d54c9bcf18d91/docs/PCIDSSPatternforOCP.md

Some teams have decided to host PCI-scoped applications on-prem (non-OpenShift) or on a cloud based service (AWS, Azure, etc) to avoid linkages with government systems not under their control.

**Training/Support**

The platform services team provides training to onboarding teams, as well as support for issues experienced.  Ministry staff that work with devops teams are also encouraged to attend training.

***Training:***
- https://cloud.gov.bc.ca/private-cloud/support-and-community/platform-training-and-resources/openshift-101/
- https://cloud.gov.bc.ca/private-cloud/support-and-community/platform-training-and-resources/openshift-201/
- https://github.com/bcdevops/devops-platform-workshops

***Support:***
- https://cloud.gov.bc.ca/private-cloud/support-and-community/how-to-get-support-or-help/
- https://cloud.gov.bc.ca/private-cloud/support-and-community/devops-requests-in-the-bc-gov-private-cloud-paas/
- https://cloud.gov.bc.ca/private-cloud/support-and-community/platform-training-and-resources/how-to-videos-and-demos/
- Various Rocket Chat channels

**App security self assessment:**
- https://beta-docs.developer.gov.bc.ca/security-best-practices-for-apps/

**Best practices for building apps on OpenShift:**
- https://beta-docs.developer.gov.bc.ca/

**Contact**
For all other matters concerning security on the OpenShift Container Platform, please contact PlatformServicesTeam@gov.bc.ca.

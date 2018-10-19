# Cockpit-APIs
Sample code for the SAP HANA Cockpit POST/GET API tutorial videos with links to the documentation. 

![SAP HANA Academy](https://yt3.ggpht.com/-BHsLGUIJDb0/AAAAAAAAAAI/AAAAAAAAAVo/6_d1oarRr8g/s100-mo-c-c0xffffffff-rj-k-no/photo.jpg)

## Purpose
SAP HANA cockpit provides modifying (POST) and nonmodifying (GET) REST APIs. This allows you to query cockpit for registered resources and register resources programmatically. Additionally, you can create resource groups, add  resources to groups, create cockpit users and add user to groups. 

### Postman 

In the examples below, I will be using [Postman](https://www.getpostman.com). This tool allows you to test your POST and GET API configuration before generating the code snippets for cURL, Go, Java, NodeJS, Python,etc.

### First Things First

Before you can make your first API call, you need four items:
* Cockpit Manager URL 
* Service Key
* OAuth Token
* GET or POST API

### Cockpit Manager URL 

Getting the Cockpit Manager URL can be as easy as looking in the address bar of your browser when you are connected. However, should this not be an option, you can query the XS controller. Below some sample output. 
``` 
h4cadm@cockpithost:/usr/sap/H4C/HDB96> xs l

API_URL: https://cockpithost.mydomain.com:39630
USERNAME: cockpit_admin
PASSWORD>
Authenticating...
ORG: HANACockpit
SPACE: SAP
API endpoint:   https://cockpithost.mydomain.com:39630 (API version: 1)
User:           cockpit_admin
Org:            HANACockpit
Space:          SAP
``` 
List the cockpit apps and filter on the Cockpit Manager
```  
xs a | grep cockpit-adminui-svc
cockpit-adminui-svc STARTED 1/1 128 MB   https://cockpithost.mydomain.com:51025
```  
### Service Key

In general, you need service keys when you want a set of credentials for use by clients other than the application in the same (XSA/CF) space. You only need to do this once. 
```  
xs csk cockpit-uaa postman-cockpit-uaa
```  
Ideally, you would want your external tool to progamatically get the OAuth Token as it expires every 4 hours (default settings). Alternatively, or for testing purposes, you can save the service key to a file (remove the lines before and after the curly brackets) and use it as input. 
```  
xs sk cockpit-uaa postman-cockpit-uaa > postman-service-key.json  
```  
Sample service key file:
```  
{
  "tenantmode" : "dedicated",
  "clientid" : "sb-cockpit!i1",
  "verificationkey" : "-----BEGIN PUBLIC KEY----- <string> -----END PUBLIC KEY-----",
  "xsappname" : "cockpit!i1",
  "identityzone" : "uaa",
  "identityzoneid" : "uaa",
  "clientsecret" : "EuQYCG8i2zZlflAQHv4Xod39XlkI/JEgSAXeOBuOtITL//UNucK5F3e0nzRtgwXvI+8Xu6NzGl14\nkyQXxp/T0w==",
  "url" : "https://cockpithost.mydomain.com:39632/uaa-security"
}
```  

### OAuth Token
For each GET or POST call, you need to provide a token, which can be obtained by POSTing the client ID and Client Secret from the service key above for authentication together with the username and password of the cockpit user and grant_type=password parameter in the body.  
The token expires every 14399 seconds (4 hours). 

Sample cURL script
```  
curl -X POST \
  https://cockpithost.mydomain.com:39632/uaa-security/oauth/token \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Postman-Token: fb459c25-4102-48eb-9635-ead91c2e0baa' \
  -H 'cache-control: no-cache' \
  -d 'grant_type=password&username=cockpit_admin&password=Abc123DoReMi'
```  
### GET and POST APIs
Currently three GET APIs are available: 
* /resource/RegisteredResourcesGet
* /group/GroupsForUserGet
* /group/GroupResourcesGet?groupId=

The RegisteredResourcesGet API supports query parameters in OData format, e.g $count, $top=, $skip=, $orderby=.
``` 
curl -X GET \
  'https://cockpithost.mydomain.com:51025/resource/RegisteredResourcesGet/$count' \
  -H 'Postman-Token: 3c34eb11-d30b-4425-822c-c25e42e8908b' \
  -H 'cache-control: no-cache'
``` 
Currently ten POST APIs are available, each with different input parameters, response, and required role. For the complete list, see the SAP HANA Cockpit documentation. Below an example fo the /group/GroupDelete API with the groupID parameter provided in JSON format. 
``` 
curl -X POST \
  https://cockpithost.mydomain.com51025/group/GroupDelete \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: e306ca66-de9b-4ca1-9682-11d22757a941' \
  -H 'cache-control: no-cache' \
  -d '{
    "groupId": "356"
}'
``` 
### Tutorial Video ### 
[![Cockpit APIs](https://img.youtube.com/vi/wuy8eTRU_EE/0.jpg)](https://www.youtube.com/watch?v=wuy8eTRU_EE "Cockpit APIs")

### Tutorial Video ### 
[![Cockpit POST APIs](https://img.youtube.com/vi/zj5qnJeR8Z0/0.jpg)](https://www.youtube.com/watch?v=zj5qnJeR8Z0 "Cockpit POST APIs")

### Documentation ### 
* [SAP HANA Administration with SAP HANA Cockpit: Managing Resources, Users, and Groups with the Cockpit APIs](https://help.sap.com/viewer/afa922439b204e9caf22c78b6b69e4f2/latest/en-US/0c2309ff879e4174a21fd342f137e8e0.html)
* [Cloud Foundry Concepts: User Account and Authentication (UAA) Server](https://docs.cloudfoundry.org/concepts/architecture/uaa.html)
* [Cloud Foundry Dev Guide: Managing Service Keys](https://docs.cloudfoundry.org/devguide/services/service-keys.html)

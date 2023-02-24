---
layout: default
title: CUCM AXL  
description: Cisco CUCM Scripting with AXL
has_toc: false
nav_order: 6
parent: Miscellaneous
permalink: /misc/ccm
---

# CUCM Scripting with AXL
{: .no_toc }
Notes on how to set up and use CUCM for scripting capabilities

### Resources
{: .no_toc }
- [AXL Schema Reference](https://developer.cisco.com/docs/axl-schema-reference/)
- You could also use the Python SDK for Cisco CUCM AXL API, [ciscoaxl](https://github.com/levensailor/ciscoaxl)

---

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


---

## Enable AXL SOAP Service on CUCM:

- Enable the AXL SOAP interface
- Browse to the CUCM Serviceability page on https://(IP_CUCM)/ccmservice
- Tools > Service Activation:
- Enable the "Cisco AXL Web Service"
![](../assets/cucm/axl.png)

---

### Create an AXL Service Account

> Step 1 - Create an AXL User Group

- CUCM > User Management > User Settings > Access Control Group.

> Step 2 - Assign the AXL role to the group

-  On the top right drop down list "Related Links". 

- Select "Assign Role to User Group" and select "Standard AXL API Access"

![](../assets/cucm/access.png)

> Step 3 - Create a new Application User

- CUCM > User Management > Application User > Add.

![](../assets/cucm/appUser.png)

- Add the User Group "AXL Group" to this user so that after saving the roles of the new Application User appear as in the following screen:

![](../assets/cucm/userRoles.png)

---

### Downloading WSDL file

- Log into the Cisco Unified CM Administration application.
- Go to **Application > Plugins**
- Click on the Download link by the Cisco CallManager **AXL SQL Toolkit Plugin**.
- The **axlsqltoolkit.zip** file contains the complete schema definition for different versions of Cisco Unified CM. The important files for each version are:
    - AXLAPI.wsdl
    - AXLEnums.xsd
    - axlmessage.xsd
    - axlsoap.xsd
    - axl.xsd

![](../assets/cucm/downloadPlugin.png)

---

## Using Python with the Zeep library
The CUCM AXL utilizes the SOAP standard for web services. Python can be used along with the `zeep` library in order to boil down the amount of XML that an admin has to deal with. When developing remember to use the [AXL Schema Reference](https://developer.cisco.com/docs/axl-schema-reference/), or even better load the wsdl file into SOAPUI and you can view sample requests.
![](../assets/cucm/soapUI.png)

---

### Boilerplate Code
Below is the boilerplate code for using the `zeep` library

```python
# -*- coding: utf-8 -*-
 
from zeep import Client, Settings
from zeep.cache import SqliteCache
from zeep.transports import Transport
from zeep.exceptions import Fault
from zeep.plugins import HistoryPlugin
from requests import Session
from requests.auth import HTTPBasicAuth
from urllib3 import disable_warnings
from urllib3.exceptions import InsecureRequestWarning
from lxml import etree
 
 
disable_warnings(InsecureRequestWarning)
 
username = 'AXL'
password = 'admin123'
# If you're not disabling SSL verification, host should be the FQDN of the server rather than IP
host = '10.10.20.1'
 
wsdl = '/Users/Tanner/scripts/12.5/AXLAPI.wsdl'
location = 'https://{host}:8443/axl/'.format(host=host)
binding = "{http://www.cisco.com/AXLAPIService/}AXLAPIBinding"
 
# Create a custom session to disable Certificate verification.
# In production you shouldn't do this, 
# but for testing it saves having to have the certificate in the trusted store.
session = Session()
session.verify = False
session.auth = HTTPBasicAuth(username, password)

settings = Settings(strict=False)
 
transport = Transport(cache=SqliteCache(), session=session, timeout=20)
history = HistoryPlugin()
client = Client(wsdl=wsdl, transport=transport, plugins=[history], settings=settings)
service = client.create_service(binding, location)
 
#Function to decode errors
def show_history():
    for item in [history.last_sent, history.last_received]:
        print(etree.tostring(item["envelope"], encoding="unicode", pretty_print=True))
```

---

### Example request
Using the AXL schema reference methods can be called from `service` as in below

```python
try:
    resp = service.updatePhone(name = 'SEP0023AF454321', 
                                    description='John Doe - 1102 - IT',
                                    lines = {"line" : {"index": 1,
                                                        "label": "John Doe Label",
                                                        "display": "John Doe Display",
                                                        "displayAscii": "John Doe ASCII",
                                                        "e164Mask": "1234561102",
                                                         "dirn": {"pattern": "1102",
                                                                  "routePartitionName": "Global Learned E164 Numbers"}
                                                      }
                                                    
                                            }
                             )
    print(resp)
except Fault:
    show_history()
```

- The methods called from `service` align directly with the AXL scheme get/requests 
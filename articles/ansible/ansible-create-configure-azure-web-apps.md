---
title: Oktatóanyag – alkalmazások konfigurálása Azure App Service Ansible használatával
description: Megtudhatja, hogyan hozhat létre alkalmazást a Azure App Service Java 8 és a Tomcat Container Runtime használatával
keywords: ansible, azure, devops, bash, forgatókönyv, Azure App Service, webalkalmazás, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156566"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Oktatóanyag: alkalmazások konfigurálása Azure App Service Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Alkalmazás létrehozása Azure App Service Java 8 és a Tomcat Container Runtime futtatásával
> * Azure Traffic Manager-profil létrehozása
> * Traffic Manager végpont definiálása a létrehozott alkalmazás használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Alapszintű app Service létrehozása

Az ebben a szakaszban szereplő forgatókönyv-kód a következő erőforrásokat határozza meg:

* Azure-erőforráscsoport, amelyen belül a App Service terv és alkalmazás telepítve van
* App Service Linuxon a Java 8 és a Tomcat Container Runtime futtatásával

Mentse a következő forgatókönyvet `firstwebapp.yml` néven:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook firstwebapp.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Alkalmazás létrehozása és az Azure Traffic Manager használata

Az [Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) lehetővé teszi annak szabályozását, hogy a webes ügyfelektől érkező kérések hogyan legyenek kiosztva a Azure app Service alkalmazásokban. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, a Traffic Manager nyomon követi az App Service-alkalmazások állapotát. Az állapotok lehetnek: fut, leállítva és törölve. A Traffic Manager segítségével eldöntheti, hogy mely végpontoknak kell fogadnia a forgalmat.

Az App Service-ben az alkalmazások [App Service-csomagban](/azure/app-service/overview-hosting-plans) futnak. Az App Service-csomag számítási erőforrásokat határoz meg az alkalmazás futtatásához. Különböző csoportokban kezelheti az App Service-csomagot és a webalkalmazást.

Az ebben a szakaszban szereplő forgatókönyv-kód a következő erőforrásokat határozza meg:

* Az Azure-erőforráscsoport, amelyen belül a App Service-csomag telepítve van
* App Service-csomag
* Az Azure-erőforráscsoport, amelyen belül az alkalmazás telepítve van
* App Service Linuxon a Java 8 és a Tomcat Container Runtime futtatásával
* Traffic Manager-profil
* Traffic Manager végpont a létrehozott alkalmazás használatával

Mentse a következő forgatókönyvet `webapp.yml` néven:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook webapp.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: alkalmazások méretezése Azure App Service Ansible használatával](/azure/ansible/ansible-scale-azure-web-apps)
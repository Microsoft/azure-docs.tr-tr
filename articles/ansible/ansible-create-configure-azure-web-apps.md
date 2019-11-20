---
title: Öğretici-Azure App Service kullanarak uygulamaları yapılandırma
description: Java 8 ve Tomcat kapsayıcı çalışma zamanı ile Azure App Service bir uygulama oluşturmayı öğrenin
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web Uygulaması, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156562"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Öğretici: uygulamaları Azure App Service kullanarak yapılandırma

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Java 8 ve Tomcat kapsayıcı çalışma zamanı ile Azure App Service bir uygulama oluşturma
> * Azure Traffic Manager profili oluşturma
> * Oluşturulan uygulamayı kullanarak Traffic Manager uç noktası tanımlama

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Temel bir App Service oluşturun

Bu bölümdeki PlayBook kodu aşağıdaki kaynakları tanımlar:

* App Service planının ve uygulamanın dağıtıldığı Azure Kaynak grubu
* Linux üzerinde Java 8 ve Tomcat kapsayıcı çalışma zamanı ile App Service

Aşağıdaki playbook'u `firstwebapp.yml` olarak kaydedin:

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

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook firstwebapp.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

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

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Uygulama oluşturma ve Azure Traffic Manager kullanma

[Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) , Web istemcilerinden gelen isteklerin Azure App Service uygulamalara nasıl dağıtıldığını denetlemenize olanak sağlar. App Service uç noktaları bir Azure Traffic Manager profiline eklendiğinde, Traffic Manager, App Service uygulamalarınızın durumunu izler. Durumlar arasında çalışıyor, durduruldu ve silindi yer alır. Traffic Manager, trafiği hangi uç noktaların alacağını belirlemek için kullanılır.

App Service'teki uygulamalar bir [App Service planında](/azure/app-service/overview-hosting-plans) çalışır. App Service planı, bir uygulamanın çalışması için bir işlem kaynakları kümesi tanımlar. App Service planınızı ve web uygulamanızı farklı gruplarda yönetebilirsiniz.

Bu bölümdeki PlayBook kodu aşağıdaki kaynakları tanımlar:

* App Service planının dağıtıldığı Azure Kaynak grubu
* App Service planı
* Uygulamanın dağıtıldığı Azure Kaynak grubu
* Linux üzerinde Java 8 ve Tomcat kapsayıcı çalışma zamanı ile App Service
* Traffic Manager profili
* Oluşturulan uygulamayı kullanarak uç noktası Traffic Manager

Aşağıdaki playbook'u `webapp.yml` olarak kaydedin:

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

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook webapp.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

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

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Öğretici: Azure App Service uygulamaları kullanarak ölçeklendirin](/azure/ansible/ansible-scale-azure-web-apps)
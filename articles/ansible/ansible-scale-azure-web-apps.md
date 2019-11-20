---
title: Öğretici-Azure App Service kullanarak uygulamaları ölçeklendirin
description: Azure App Service bir uygulamayı nasıl ölçeklendireceğinizi öğrenin
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, Azure App Service, Web uygulaması, ölçek, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155921"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Öğretici: Azure App Service uygulamaları kullanarak ölçeklendirin

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Mevcut bir App Service planının olguları alın
> * App Service planını üç çalışan ile S2 'ye kadar ölçeklendirin

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service App** -bir Azure App Service uygulamanız yoksa, [Azure App Service içinde bir uygulamayı yapılandırmak için yapılandırın](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Bir uygulamayı ölçeklendirme

Ölçeklendirme için iki iş akışı vardır: *ölçeği artırma* ve *genişletme*.

**Ölçeği artırma:** Ölçeği genişletmek için daha fazla kaynak elde etmek üzere. Bu kaynaklar CPU, bellek, disk alanı, VM 'Ler ve daha fazlasını içerir. Uygulamanın ait olduğu App Service planının fiyatlandırma katmanını değiştirerek bir uygulamayı ölçeklendirebilirsiniz. 
**Ölçeği genişletme:** Ölçeği genişletmek için uygulamanızı çalıştıran VM örneği sayısını artırın. App Service planı fiyatlandırma katmanınıza bağlı olarak, 20 örneğe kadar ölçeği değiştirebilirsiniz. [Otomatik ölçeklendirme](/azure/azure-monitor/platform/autoscale-get-started) , ön tanımlı kurallara ve zamanlamalara göre örnek sayısını otomatik olarak ölçeklendirmenize olanak tanır.

Bu bölümdeki PlayBook kodu aşağıdaki işlemi tanımlar:

* Mevcut bir App Service planının olguları alın
* App Service planını üç çalışan ile S2 'ye güncelleştirme

Aşağıdaki playbook'u `webapp_scaleup.yml` olarak kaydedin:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook webapp_scaleup.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure üzerinde Ansible](/azure/ansible/)
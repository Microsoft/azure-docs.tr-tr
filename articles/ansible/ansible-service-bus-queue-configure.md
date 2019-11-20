---
title: Öğretici-Azure Service Bus kullanarak kuyrukları yapılandırma
description: Azure Service Bus kuyruğu oluşturmak için nasıl kullanılacağını öğrenin
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, hizmet veri yolu, kuyruk
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9903419a52be61cd7ec74214858bce81df7e727e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155816"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Öğretici: Azure Service Bus kullanarak kuyrukları yapılandırma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Kuyruk oluşturma
> * SAS plicy oluşturma
> * Ad alanı bilgilerini al
> * Kuyruk bilgilerini al
> * Kuyruk SAS ilkesini iptal etme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Service Bus kuyruğu oluşturma

Örnek PlayBook kodu aşağıdaki kaynakları oluşturur:
- Azure kaynak grubu
- Kaynak grubu içinde Service Bus ad alanı
- Ad alanı ile Service Bus kuyruğu

Aşağıdaki playbook'u `servicebus_queue.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>SAS ilkesi oluşturma

[Paylaşılan erişim imzası (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) , belirteçleri kullanan, talep tabanlı bir yetkilendirme mekanizmasıdır. 

Örnek PlayBook kodu, farklı ayrıcalıklara sahip bir Service Bus kuyruğu için iki SAS ilkesi oluşturur.

Aşağıdaki playbook'u `servicebus_queue_policy.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:
- `rights` değeri, bir kullanıcının kuyrukla olan ayrıcalığını temsil eder. Şu değerlerden birini belirtin: `manage`, `listen`, `send`veya `listen_send`.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Ad alanı bilgilerini al

Örnek PlayBook kodu, ad alanı bilgilerini sorgular.

Aşağıdaki playbook'u `servicebus_namespace_info.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:
- `show_sas_policies` değeri, belirtilen ad alanı altında SAS ilkelerinin gösterilip gösterilmeyeceğini gösterir. Varsayılan olarak, ek ağ yükünün önüne geçmek için değeri `False`.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Kuyruk bilgilerini al

Örnek PlayBook kodu, kuyruk bilgilerini sorgular. 

Aşağıdaki playbook'u `servicebus_queue_info.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:
- `show_sas_policies` değeri, SAS ilkelerinin belirtilen sıra altında gösterilip gösterilmeyeceğini gösterir. Bu değer, varsayılan olarak, ek ağ yükünün önüne geçmek için `False` olarak ayarlanır.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Kuyruk SAS ilkesini iptal etme

Örnek PlayBook kodu bir sıra SAS ilkesini siler.

Aşağıdaki playbook'u `servicebus_queue_policy_delete.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki kodu `cleanup.yml`olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"] 
> [Öğretici: Azure Service Bus bir konuyu, anormal kullanarak yapılandırma](ansible-service-bus-topic-configure.md)
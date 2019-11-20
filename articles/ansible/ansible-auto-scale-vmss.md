---
title: Öğretici-Azure 'da sanal makine ölçek kümelerini Anerişilebilir kullanarak otomatik ölçeklendirme
description: Azure 'da otomatik ölçeklendirme ile sanal makine ölçek kümelerini ölçeklendirmek için nasıl kullanılacağını öğrenin
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, ölçek, otomatik ölçeklendirme, sanal makine, sanal makine ölçek kümesi, VMSS
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156823"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Öğretici: Azure 'da sanal makine ölçek kümelerini anormal kullanarak otomatik ölçeklendirme

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Sanal makine örneklerinin sayısını otomatik olarak ayarlamaya yönelik özelliği otomatik [ölçeklendirme](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)olarak adlandırılır. Otomatik ölçeklendirme avantajı, uygulamanızın performansını izlemek ve iyileştirmek için yönetim yükünü azaltmasıdır. Otomatik ölçeklendirme, isteğe veya tanımlı bir zamanlamaya göre yanıt olarak yapılandırılabilir. Anormal bir şekilde kullanıldığında, olumlu bir müşteri deneyimi için kabul edilebilir performansı tanımlayan otomatik ölçeklendirme kurallarını belirtebilirsiniz.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Otomatik ölçeklendirme profilini tanımlama
> * Yinelenen bir zamanlamaya göre otomatik ölçeklendirme
> * Uygulama performansına göre otomatik ölçeklendirme
> * Otomatik ölçeklendirme ayarları bilgilerini al 
> * Otomatik ölçeklendirme ayarını devre dışı bırakma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Zamanlamaya göre otomatik ölçeklendirme

Bir ölçek kümesinde otomatik ölçeklendirmeyi etkinleştirmek için ilk olarak bir otomatik ölçeklendirme profili tanımlamanız gerekir. Bu profil varsayılan, en düşük ve en yüksek ölçek kümesi kapasitesini tanımlar. Bu sınırlar, sanal makine örneklerini sürekli olarak oluşturmadığından maliyeti denetlemenizi sağlar ve ölçek genişletme olayında kalan minimum örnek sayısı ile kabul edilebilir performansı dengelemenize olanak tanır. 

Anormal, ölçek kümelerinizi belirli bir tarih veya yinelenen zamanlamaya göre ölçeklendirmenize imkan tanır.

Bu bölümdeki PlayBook kodu, sanal makine örneklerinin sayısını her Pazartesi 10:00 ' de üç olarak artırır.

Aşağıdaki playbook'u `vmss-auto-scale.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Performans verilerine göre otomatik ölçeklendirme

Uygulamanızın talebi arttıkça, ölçek kümelerinizin sanal makine örnekleri üzerindeki yük artar. Bu kısa süreli bir talep olmayıp tutarlı şekilde yük artıyorsa, ölçek kümesindeki sanal makine örneği sayısını artırmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Bu sanal makine örnekleri oluşturulduğunda ve uygulamalarınız dağıtıldığında ölçek kümesi, yük dengeleyici aracılığıyla bunlara trafiği dağıtmaya başlar. Anormal, CPU kullanımı, disk kullanımı ve uygulama yükleme süresi gibi hangi ölçümlerin izleneceğini denetlemenizi sağlar. Performans ölçümü eşiklerine, yinelenen bir zamanlamaya göre veya belirli bir tarihe göre ölçek kümelerinde ölçeklendirebilir ve ölçek getirebilirsiniz. 

Bu bölümdeki PlayBook kodu, Pazartesi günü 18:00 ' de önceki 10 dakikalık CPU iş yükünü denetler. 

PlayBook, CPU yüzdesi ölçümlerine bağlı olarak aşağıdaki eylemlerden birini yapar:

- Sanal makine örneği sayısını dört olarak ölçeklendirir
- VM örneği sayısında bir tane olarak ölçeklendirir

Aşağıdaki playbook'u `vmss-auto-scale-metrics.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Otomatik ölçeklendirme ayarları bilgilerini al 

Bu bölümdeki PlayBook kodu, otomatik ölçeklendirme ayarının ayrıntılarını almak için `azure_rm_autoscale_facts` modülünü kullanır.

Aşağıdaki playbook'u `vmss-auto-scale-get-settings.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Otomatik ölçeklendirme ayarlarını devre dışı bırak

Otomatik ölçeklendirme ayarlarını devre dışı bırakmak için iki yol vardır. Bunlardan biri, `true` `enabled` anahtarını `false`olarak değiştirmenin bir yoludur. İkinci yöntem ayarı silmektir.

Bu bölümdeki PlayBook kodu, otomatik ölçeklendirme ayarını siler. 

Aşağıdaki playbook'u `vmss-auto-scale-delete-setting.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Öğretici: anormal kullanarak Azure sanal makine ölçek kümelerinin özel görüntüsünü güncelleştirme](./ansible-vmss-update-image.md)
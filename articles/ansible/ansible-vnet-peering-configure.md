---
title: Öğretici-Azure sanal ağ eşlemesini, anormal kullanarak yapılandırma
description: Sanal ağ eşlemesi ile sanal ağları bağlamak için nasıl kullanılacağını öğrenin.
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, ağ, eşleme
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155736"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Öğretici: Azure sanal ağ eşlemesini, anormal kullanarak yapılandırma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Sanal ağ (VNet) eşlemesi](/azure/virtual-network/virtual-network-peering-overview) , iki Azure sanal ağını sorunsuzca bağlamanıza olanak tanır. Eşlendikten sonra iki sanal ağ bağlantı amaçlarıyla bir tane olarak görünür. 

Trafik, özel IP adresleri aracılığıyla aynı sanal ağdaki VM 'Ler arasında yönlendirilir. Benzer şekilde, eşlenmiş bir sanal ağdaki VM 'Ler arasındaki trafik, Microsoft omurga altyapısı aracılığıyla yönlendirilir. Sonuç olarak, farklı sanal ağlardaki VM 'Ler birbirleriyle iletişim kurabilir.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * İki sanal ağ oluşturma
> * İki sanal ağı eşler
> * İki ağ arasındaki eşlemeyi silme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>İki kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- İki kaynak grubu oluşturma 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>İlk sanal ağı oluşturma

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Sanal ağ oluşturma
- Sanal ağ içinde bir alt ağ oluşturma

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>İkinci sanal ağı oluşturma

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Sanal ağ oluşturma
- Sanal ağ içinde bir alt ağ oluşturma

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>İki sanal ağı eşler

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Sanal Ağ eşlemesini Başlat
- Daha önce oluşturulmuş ikinci sanal ağ

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Sanal Ağ eşlemesini Sil

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Daha önce oluşturulan iki sanal ağ arasındaki eşlemeyi Sil

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Örnek PlayBook 'u alın

Örnek PlayBook 'un tamamını almanın iki yolu vardır:

- [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) ve `vnet_peering.yml`kaydedin.
- `vnet_peering.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Örnek PlayBook 'u çalıştırma

Bu bölümdeki örnek PlayBook kodu, bu öğretici genelinde gösterilen çeşitli özellikleri test etmek için kullanılır.

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- `vars` bölümünde, `{{ resource_group_name }}` yer tutucusunu kaynak grubunuzun adıyla değiştirin.

Anerişilebilir-PlayBook komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vnet_peering.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Daha önce oluşturulan iki kaynak grubunu sil

Aşağıdaki playbook'u `cleanup.yml` olarak kaydedin:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- `{{ resource_group_name-1 }}` yer tutucusunu oluşturulan ilk kaynak grubunun adıyla değiştirin.
- `{{ resource_group_name-2 }}` yer tutucusunu, oluşturulan ikinci kaynak grubunun adıyla değiştirin.
- Belirtilen iki kaynak grubu içindeki tüm kaynaklar silinecek.

Anerişilebilir-PlayBook komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure üzerinde Ansible](/azure/ansible/)
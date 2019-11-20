---
title: Öğretici-Azure yol tablolarını anormal kullanarak yapılandırma
description: Anormal kullanarak Azure yol tabloları oluşturmayı, yönetmeyi ve silmeyi öğrenin. Ayrıca yollar oluşturmayı ve silmeyi de öğrenin.
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, ağ, rota, yol tablosu
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d1e44a98405bc1009f6f3d56d90fc1fd655d77d5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156497"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Öğretici: Azure yol tablolarını anormal kullanarak yapılandırma

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure, Azure alt ağları, sanal ağlar ve şirket içi ağlar arasındaki trafiği otomatik olarak yönlendirir. Ortamınızın yönlendirmesi üzerinde daha fazla denetime ihtiyacınız varsa, bir [rota tablosu](/azure/virtual-network/virtual-networks-udr-overview)oluşturabilirsiniz. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Yol tablosu oluşturma bir sanal ağ ve alt ağ oluşturma bir yönlendirme tablosunu alt ağ ile Ilişkilendirme rota tablosunun alt ağ ile Ilişkisini kaldırma rotalar oluşturma ve silme rota tablosunu sorgulama rota tablosunu silme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Yönlendirme tablosu oluşturma

Bu bölümdeki PlayBook kodu bir yol tablosu oluşturur. Rota tablosu limitleri hakkında daha fazla bilgi için bkz. [Azure Limitleri](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Aşağıdaki playbook'u `route_table_create.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Yönlendirme tablosunu bir alt ağ ile ilişkilendirme

Bu bölümdeki PlayBook kodu:

* Bir sanal ağ oluşturur
* Sanal ağ içinde bir alt ağ oluşturur
* Bir yol tablosunu alt ağla ilişkilendirir

Yol tabloları, sanal ağlarla ilişkili değildir. Bunun yerine, rota tabloları bir sanal ağın alt ağıyla ilişkilendirilir.

Sanal ağ ve yol tablosu aynı Azure konumunda ve abonelikte birlikte bulunmalıdır.

Alt ağlar ve rota tablolarının bire çok ilişkisi vardır. Bir alt ağ, ilişkili yol tablosu veya bir yol tablosu olmadan tanımlanabilir. Yol tabloları None, One veya çok alt ağlarla ilişkilendirilebilir. 

Alt ağdan gelen trafik şu temel alınarak yönlendirilir:

- rota tablolarında tanımlanan rotalar
- [Varsayılan yollar](/azure/virtual-network/virtual-networks-udr-overview#default)
- Şirket içi ağdan yayılan yollar

Sanal ağın bir Azure sanal ağ geçidine bağlı olması gerekir. Ağ Geçidi, bir VPN ağ geçidiyle BGP kullanılıyorsa ExpressRoute veya VPN olabilir.

Aşağıdaki playbook'u `route_table_associate.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Bir alt ağdan yol tablosunun ilişkilendirmesini kaldırma

Bu bölümdeki PlayBook kodu bir alt ağdan yol tablosunun ilişkisini kaldırmaktadır.

Bir alt ağdan yol tablosunu ilişkilendirme sırasında, alt ağın `route_table` `None`olarak ayarlayın. 

Aşağıdaki playbook'u `route_table_dissociate.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Yönlendirme oluşturma

Bu bölümdeki PlayBook kodu, yol tablosundaki bir yoldur. 

Aşağıdaki playbook'u `route_create.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* `virtual_network_gateway` `next_hop_type`olarak tanımlanmıştır. Azure 'un yönlendirmeleri nasıl seçtiği hakkında daha fazla bilgi için bkz. [yönlendirmeye genel bakış](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` `10.1.0.0/16`olarak tanımlanmıştır. Ön ek, yol tablosu içinde çoğaltılamaz.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Bir yolu silme

Bu bölümdeki PlayBook kodu bir rota tablosundan bir yol siler.

Aşağıdaki playbook'u `route_delete.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Rota tablosu bilgilerini al

Bu bölümdeki PlayBook kodu, yol tablosu bilgilerini almak için `azure_rm_routetable_facts` anormal modül kullanır.

Aşağıdaki playbook'u `route_table_facts.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Yol tablosunu silme

Bu bölümdeki PlayBook kodu bir yol tablosudur.

Bir yol tablosu silindiğinde, tüm rotaları de silinir.

Bir alt ağ ile ilişkili ise yol tablosu silinemez. Yol tablosunu silmeyi denemeden önce [herhangi bir alt ağdan yol tablosunun ilişkilendirmesini](#dissociate-a-route-table-from-a-subnet) kaldırın. 

Aşağıdaki playbook'u `route_table_delete.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"] 
> [Azure üzerinde Ansible](/azure/ansible/)
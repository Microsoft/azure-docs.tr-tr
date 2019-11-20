---
title: Öğretici-Azure Kubernetes Service 'te (AKS) Azure CNı ağını, anormal bir şekilde yapılandırma
description: Azure Kubernetes Service (AKS) kümesinde Kubernetes kullanan ağını yapılandırmak için nasıl kullanılacağını öğrenin
keywords: anyalabilen, Azure, DevOps, Bash, cloudshell, PlayBook, aks, Container, aks, Kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156904"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Öğretici: Azure Kubernetes Service 'te (AKS) Azure CNı ağını, anormal bir şekilde yapılandırma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS 'i kullanarak, aşağıdaki ağ modellerini kullanarak bir küme dağıtabilirsiniz:

- [Kubenet Networking](/azure/aks/configure-kubenet) -ağ kaynakları genellikle aks kümesi dağıtıldığında oluşturulur ve yapılandırılır.
- [Azure CNI ağı](/azure/aks/configure-azure-cni) -aks kümesi, var olan sanal ağ (VNet) kaynaklarına ve yapılandırmalarına bağlanır.

AKS 'deki uygulamalarınızın ağı hakkında daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için ağ kavramları](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS kümesi oluşturma
> * Azure CNI ağını yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Sanal ağ ve alt ağ oluşturma

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Sanal ağ oluşturma
- Sanal ağ içinde bir alt ağ oluşturma

Aşağıdaki playbook'u `vnet.yml` olarak kaydedin:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Sanal ağda AKS kümesi oluşturma

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Bir sanal ağ içinde AKS kümesi oluşturun.

Aşağıdaki playbook'u `aks.yml` olarak kaydedin:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- Desteklenen sürümü bulmak için `azure_rm_aks_version` modülünü kullanın.
- `vnet_subnet_id`, önceki bölümde oluşturulan alt ağıdır.
- PlayBook, `~/.ssh/id_rsa.pub``ssh_key` yükler. Bunu değiştirirseniz, "SSH-RSA" (tırnak işaretleri olmadan) ile başlayan tek satır biçimini kullanın.
- `client_id` ve `client_secret` değerleri, varsayılan kimlik bilgisi dosyası olan `~/.azure/credentials`'dan yüklenir. Bu değerleri hizmet sorumlusu olarak ayarlayabilir veya bu değerleri ortam değişkenlerinden yükleyebilirsiniz:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Örnek PlayBook 'u çalıştırma

Bu bölümdeki örnek PlayBook kodu, bu öğretici genelinde gösterilen çeşitli özellikleri test etmek için kullanılır.

Aşağıdaki playbook'u `aks-azure-cni.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- `aksansibletest` değerini kaynak grubu adı olarak değiştirin.
- `aksansibletest` değerini AKS adınızla değiştirin.
- `eastus` değerini kaynak grubu konumuyla değiştirin.

Anerişilebilir-PlayBook komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook aks-azure-cni.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- `vars` bölümünde başvurulan bir kaynak grubunu silin.

Aşağıdaki playbook'u `cleanup.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- `{{ resource_group_name }}` yer tutucusunu kaynak grubunuzun adıyla değiştirin.
- Belirtilen kaynak grubundaki tüm kaynaklar silinecek.

Anerişilebilir-PlayBook komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: anormal kullanarak AKS 'de Azure Active Directory yapılandırma](./ansible-aks-configure-rbac.md)
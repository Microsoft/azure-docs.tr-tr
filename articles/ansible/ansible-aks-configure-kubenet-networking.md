---
title: Öğretici-Azure Kubernetes Service 'te (AKS) Kubernetes kullanan ağını, anormal bir şekilde yapılandırın
description: Azure Kubernetes Service (AKS) kümesinde Kubernetes kullanan ağını yapılandırmak için nasıl kullanılacağını öğrenin
keywords: anyalabilen, Azure, DevOps, Bash, cloudshell, PlayBook, aks, Container, aks, Kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156885"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Öğretici: Azure Kubernetes hizmeti 'nde (AKS) Kubernetes kullanan ağını, anormal olarak yapılandırma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS 'i kullanarak, aşağıdaki ağ modellerini kullanarak bir küme dağıtabilirsiniz:

- [Kubenet Networking](/azure/aks/configure-kubenet) -ağ kaynakları genellikle aks kümesi dağıtıldığında oluşturulur ve yapılandırılır.
- [Azure Container Network Interface (CNı) ağı](/azure/aks/configure-azure-cni) -aks kümesi, var olan sanal ağ kaynaklarına ve yapılandırmalara bağlanır.

AKS 'deki uygulamalarınızın ağı hakkında daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için ağ kavramları](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS kümesi oluşturma
> * Azure Kubernetes kullanan ağını yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Sanal ağ ve alt ağ oluşturma

Bu bölümdeki PlayBook kodu aşağıdaki Azure kaynaklarını oluşturur:

- Sanal ağ
- Sanal ağ içindeki alt ağ

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

Bu bölümdeki PlayBook kodu, sanal ağ içinde bir AKS kümesi oluşturur. 

Aşağıdaki playbook'u `aks.yml` olarak kaydedin:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- Desteklenen sürümü bulmak için `azure_rm_aks_version` modülünü kullanın.
- `vnet_subnet_id`, önceki bölümde oluşturulan alt ağıdır.
- `network_profile`, Kubernetes kullanan Network eklentisinin özelliklerini tanımlar.
- `service_cidr`, AKS kümesindeki iç Hizmetleri bir IP adresine atamak için kullanılır. Bu IP adresi aralığı, ağınızda başka bir yerde kullanılmamış bir adres alanı olmalıdır. 
- `dns_service_ip` adresi, hizmet IP adresi aralığınızı ". 10" adresi olmalıdır.
- `pod_cidr`, ağ ortamınızda başka bir yerde kullanımda olmayan büyük bir adres alanı olmalıdır. Adres aralığı, ölçeğini genişletmek istediğiniz düğüm sayısına uyacak kadar büyük olmalıdır. Küme dağıtıldıktan sonra bu adres aralığını değiştiremezsiniz.
- Pod IP adresi aralığı, kümedeki her düğüme/24 adres alanı atamak için kullanılır. Aşağıdaki örnekte, 192.168.0.0/16 `pod_cidr` ilk düğüm olan 192.168.0.0/24, ikinci düğüm 192.168.1.0/24 ve üçüncü düğüm 192.168.2.0/24 düğümünü atar.
- Küme ölçeklenirken veya yükseltildikten sonra, Azure her yeni düğüme bir pod IP adres aralığı atamaya devam eder.
- PlayBook, `~/.ssh/id_rsa.pub``ssh_key` yükler. Bunu değiştirirseniz, "SSH-RSA" (tırnak işaretleri olmadan) ile başlayan tek satır biçimini kullanın.
- `client_id` ve `client_secret` değerleri, varsayılan kimlik bilgisi dosyası olan `~/.azure/credentials`'dan yüklenir. Bu değerleri hizmet sorumlusu olarak ayarlayabilir veya bu değerleri ortam değişkenlerinden yükleyebilirsiniz:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Ağ kaynaklarını ilişkilendir

Bir AKS kümesi oluşturduğunuzda, bir ağ güvenlik grubu ve yol tablosu oluşturulur. Bu kaynaklar AKS tarafından yönetilir ve hizmetleri oluşturup kullanıma sundığınızda güncelleştirilir. Ağ güvenlik grubu ve yol tablosunu, sanal ağ alt ağınızla aşağıdaki şekilde ilişkilendirin. 

Aşağıdaki PlayBook 'u `associate.yml`olarak kaydedin.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- `node_resource_group`, AKS düğümlerinin oluşturulduğu kaynak grubunun adıdır.
- `vnet_subnet_id`, önceki bölümde oluşturulan alt ağ olur.


## <a name="run-the-sample-playbook"></a>Örnek PlayBook 'u çalıştırma

Bu bölümde, bu makalede oluşturulan görevleri çağıran tüm örnek PlayBook listelenmektedir. 

Aşağıdaki playbook'u `aks-kubenet.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

`vars` bölümünde aşağıdaki değişiklikleri yapın:

- `resource_group` anahtarı için `aksansibletest` değerini kaynak grubu adınızla değiştirin.
- `name` anahtarı için `aksansibletest` değerini AKS adınızla değiştirin.
- `Location` anahtarı için `eastus` değerini kaynak grubu konumınızla değiştirin.

`ansible-playbook` komutunu kullanarak tüm PlayBook 'ları çalıştırın:

```bash
ansible-playbook aks-kubenet.yml
```

PlayBook 'un çalıştırılması aşağıdaki çıktıya benzer sonuçları gösterir:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki kodu `cleanup.yml`olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

`vars` bölümünde, `{{ resource_group_name }}` yer tutucusunu kaynak grubunuzun adıyla değiştirin.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici-Azure Container Networking Interface (CNı) ağ iletişimini kullanarak AKS 'de yapılandırma](./ansible-aks-configure-cni-networking.md)
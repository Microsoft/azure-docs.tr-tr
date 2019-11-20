---
title: Öğretici-Azure 'da sanal makine ölçek kümelerini, anormal kullanarak yapılandırma
description: Azure 'da sanal makine ölçek kümeleri oluşturmak ve yapılandırmak için nasıl kullanılacağını öğrenin
keywords: ansible, azure, devops, bash, playbook, sanal makine, sanal makine ölçek kümesi, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156542"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Öğretici: Azure 'da sanal makine ölçek kümelerini, anormal kullanarak yapılandırma

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * VM için kaynakları yapılandırma
> * Ölçek kümesi yapılandırma
> * Ölçek kümesini sanal makine örneklerinin artırarak ölçeklendirin 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Ölçek kümesi yapılandırma

Bu bölümdeki PlayBook kodu aşağıdaki kaynakları tanımlar:

* Tüm kaynaklarınızın dağıtılacağı **kaynak grubu** .
* 10.0.0.0/16 adres alanında **sanal ağ**
* Sanal ağ içinde **alt ağ**
* İnternet üzerindeki kaynaklara erişmenizi sağlayan **genel IP adresi**
* Ölçek kümesi içindeki ve olmayan ağ trafiği akışını denetleyen **ağ güvenlik grubu**
* Yük dengeleyici kurallarını kullanarak trafiği tanımlı bir VM'ler kümesi arasında dağıtan **yük dengeleyici**
* Oluşturulan tüm kaynakları kullanan **sanal makine ölçek kümesi**

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) ve `vmss-create.yml`kaydedin.
* `vmss-create.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* `vars` bölümünde, `{{ admin_password }}` yer tutucusunu kendi parolanızla değiştirin.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vmss-create.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Sanal makine örneklerinin sayısını görüntüleme

[Yapılandırılmış ölçek kümesinde](#configure-a-scale-set) Şu anda iki örnek vardır. Bu değeri doğrulamak için aşağıdaki adımlar kullanılır:

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. Yapılandırdığınız ölçek kümesine gidin.

1. Ölçek kümesi adını parantez içindeki örneklerin sayısıyla görürsünüz: `Standard_DS1_v2 (2 instances)`

1. Aşağıdaki komutu çalıştırarak [Azure Cloud Shell](https://shell.azure.com/) örnek sayısını da doğrulayabilirsiniz:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Cloud Shell ' de Azure CLı komutunu çalıştırmanın sonuçları üç örnek olduğunu gösterir: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Ölçek kümesinin ölçeğini genişletme

Bu bölümdeki PlayBook kodu, ölçek kümesi hakkındaki bilgileri alır ve kapasitesini iki ile üç arasında değişir.

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) ve `vmss-scale-out.yml`kaydedin.
* `vmss-scale-out.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vmss-scale-out.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Sonuçları doğrulama

Azure portal yoluyla çalışmalarınızın sonuçlarını doğrulayın:

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. Yapılandırdığınız ölçek kümesine gidin.

1. Ölçek kümesi adını parantez içindeki örneklerin sayısıyla görürsünüz: `Standard_DS1_v2 (3 instances)` 

1. Değişikliği [Azure Cloud Shell](https://shell.azure.com/) ile aşağıdaki komutu çalıştırarak da doğrulayabilirsiniz:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Cloud Shell ' de Azure CLı komutunu çalıştırmanın sonuçları üç örnek olduğunu gösterir: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Öğretici: Azure 'daki sanal makine ölçek kümelerine uygulama dağıtma (Anlabilen)](./ansible-deploy-app-vmss.md)
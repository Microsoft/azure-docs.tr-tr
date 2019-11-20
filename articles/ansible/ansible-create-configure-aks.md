---
title: Öğretici-Azure 'da Azure Kubernetes hizmeti (AKS) kümelerini kullanarak yapılandırma
description: Ansible'ı kullanarak Azure'da Azure Kubernetes Service kümesi oluşturmayı ve kullanmayı öğrenin
keywords: anyalabilen, Azure, DevOps, Bash, cloudshell, PlayBook, aks, Container, aks, Kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156689"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Öğretici: Azure 'da Azure Kubernetes hizmeti (AKS) kümelerini anormal kullanarak yapılandırma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS, Kullanıcı kimlik doğrulaması için [Azure Active Directory (ad)](/azure/active-directory/) kullanacak şekilde yapılandırılabilir. Yapılandırıldıktan sonra, AKS kümesinde oturum açmak için Azure AD kimlik doğrulama belirtecinizi kullanırsınız. RBAC, kullanıcının kimliğini veya dizin grubu üyeliğini temel alabilir.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS kümesi oluşturma
> * AKS kümesi yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Yönetilen AKS kümesi oluşturma

Örnek PlayBook, kaynak grubu içinde bir kaynak grubu ve bir AKS kümesi oluşturur.

Aşağıdaki playbook'u `azure_create_aks.yml` olarak kaydedin:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

- `tasks` içindeki ilk bölüm `eastus` konumu içinde `myResourceGroup` adlı bir kaynak grubu tanımlar.
- `tasks` içindeki ikinci bölüm `myResourceGroup` kaynak grubu içinde `myAKSCluster` adlı bir AKS kümesini tanımlar.
- `your_ssh_key` yer tutucusu için, "SSH-RSA" (tırnak işaretleri olmadan) ile başlayan RSA ortak anahtarınızı tek satırlık biçimde girin.
- `aks_version` yer tutucusu için [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) komutunu kullanın.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook azure_create_aks.yml
```

PlayBook 'un çalıştırılması aşağıdaki çıktıya benzer sonuçları gösterir:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>AKS düğümlerini ölçeklendirme

Önceki bölümde yer alan örnek playbook, iki düğüm tanımlar. `agent_pool_profiles` bloğundaki `count` değerini değiştirerek düğüm sayısını ayarlayabilirsiniz.

Aşağıdaki playbook'u `azure_configure_aks.yml` olarak kaydedin:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

- `your_ssh_key` yer tutucusu için, "SSH-RSA" (tırnak işaretleri olmadan) ile başlayan RSA ortak anahtarınızı tek satırlık biçimde girin.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook azure_configure_aks.yml
```

PlayBook 'un çalıştırılması aşağıdaki çıktıya benzer sonuçları gösterir:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Yönetilen AKS kümesini silme

Örnek PlayBook bir AKS kümesini siler.

Aşağıdaki playbook'u `azure_delete_aks.yml` olarak kaydedin:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook azure_delete_aks.yml
```

PlayBook 'un çalıştırılması aşağıdaki çıktıya benzer sonuçları gösterir:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure Kubernetes Hizmeti’nde (AKS) uygulamayı ölçeklendirme](/azure/aks/tutorial-kubernetes-scale)
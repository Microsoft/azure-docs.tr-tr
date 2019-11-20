---
title: Öğretici-Azure DevTest Labs kullanarak laboratuvarları yapılandırma
description: Azure DevTest Labs kullanarak bir laboratuvarı yapılandırma hakkında bilgi edinin
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, DevTest Labs
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b6981ee94c4d82997c574db037befb9782465d08
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156267"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Öğretici: Azure DevTest Labs ' de laboratuvarları yapılandırma ve kullanma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) , geliştiricilerin UYGULAMALARı için VM ortamlarının oluşturulmasını otomatik hale getirmenizi sağlar. Bu ortamlar uygulama geliştirme, test ve eğitim için yapılandırılabilir. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Laboratuvar oluşturma
> * Laboratuvar ilkelerini ayarlama
> * Laboratuvar zamanlamalarını ayarlama
> * Laboratuvar sanal ağını oluşturma
> * Laboratuvar için bir yapıt kaynağı tanımlama
> * Laboratuvar dahilinde VM oluşturma
> * Laboratuvarın yapıt kaynaklarını ve yapıtları listeleyin
> * Yapıt kaynakları için Azure Resource Manager bilgileri alın
> * Laboratuvar ortamını oluşturma
> * Laboratuvar görüntüsünü oluşturma
> * Laboratuvarı silme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

Örnek PlayBook kod parçacığı, bir Azure Kaynak grubu oluşturur. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Laboratuvar oluşturma

Sonraki görev, örnek Laboratuvarı oluşturur.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Laboratuvar ilkelerini ayarlama

Laboratuvar ilkesi ayarlarını ayarlayabilirsiniz. Aşağıdaki değerler ayarlanabilir:

- `user_owned_lab_vm_count`, bir kullanıcının sahip olduğu VM 'lerin sayısıdır
- `user_owned_lab_premium_vm_count`, bir kullanıcının sahip olduğu Premium VM 'lerin sayısıdır
- `lab_vm_count` en yüksek laboratuvar VM sayısıdır
- `lab_premium_vm_count` en yüksek laboratuvar Premium VM sayısıdır
- `lab_vm_size`, izin verilen laboratuvar VM 'lerinin boyutudur
- `gallery_image`, izin verilen Galeri görüntüs
- `user_owned_lab_vm_count_in_subnet`, bir alt ağdaki en fazla Kullanıcı sanal makine sayısıdır
- `lab_target_cost`, laboratuvarın hedef maliyetidir

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Laboratuvar zamanlamalarını ayarlama

Bu bölümdeki örnek görev, laboratuvar zamanlamasını yapılandırır. 

Aşağıdaki kod parçacığında, VM başlatma süresini belirtmek için `lab_vms_startup` değeri kullanılır. Benzer şekilde, `lab_vms_shutdown` değerini ayarlamak laboratuvar VM 'si kapatılma süresini belirler.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Laboratuvar sanal ağını oluşturma

Aşağıdaki görev, varsayılan laboratuvar sanal ağını oluşturur.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Laboratuvar için bir yapıt kaynağı tanımlama

Yapıt kaynağı, yapıt tanımı ve Azure Resource Manager şablonları içeren doğru şekilde yapılandırılmış bir GitHub deposudur. Her laboratuvar önceden tanımlanmış ortak yapıtlar ile gelir. Aşağıdaki görevler, bir laboratuvar için yapıt kaynağı oluşturmayı gösterir.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Laboratuvar dahilinde VM oluşturma

Laboratuvar dahilinde bir VM oluşturun.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Laboratuvarın yapıt kaynaklarını ve yapıtları listeleyin

Laboratuvardaki tüm varsayılan ve özel yapıt kaynaklarını listelemek için aşağıdaki görevi kullanın:

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Aşağıdaki görev tüm yapıtları listeler:

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Yapıt kaynakları için Azure Resource Manager bilgileri alın

`public environment repository`tüm Azure Resource Manager şablonlarını listelemek için, önceden tanımlanmış depo şablonlar:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Aşağıdaki görev, depodan belirli bir Azure Resource Manager şablonunun ayrıntılarını alır:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Laboratuvar ortamını oluşturma

Aşağıdaki görev, genel ortam deposundan şablonlardan birini temel alan laboratuvar ortamını oluşturur.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Laboratuvar görüntüsünü oluşturma

Aşağıdaki görev, bir VM 'den bir görüntü oluşturur. Görüntü, özdeş VM 'Ler oluşturmanızı sağlar.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Laboratuvarı silme

Laboratuvarı silmek için aşağıdaki görevi kullanın:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Örnek PlayBook 'u alın

Örnek PlayBook 'un tamamını almanın iki yolu vardır:
- [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) ve `devtestlab-create.yml`kaydedin.
- `devtestlab-create.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>PlayBook 'u çalıştırma

Bu bölümde, bu makalede gösterilen çeşitli özellikleri test etmek için PlayBook 'u çalıştırın.

PlayBook 'u çalıştırmadan önce aşağıdaki değişiklikleri yapın:
- `vars` bölümünde, `{{ resource_group_name }}` yer tutucusunu kaynak grubunuzun adıyla değiştirin.
- GitHub belirtecini `GITHUB_ACCESS_TOKEN`adlı bir ortam değişkeni olarak depolayın.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki kodu `cleanup.yml`olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure üzerinde Ansible](/azure/ansible/)
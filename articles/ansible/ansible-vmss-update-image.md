---
title: Öğretici-Azure sanal makine ölçek kümelerinin özel görüntüsünü Anerişilebilir kullanarak güncelleştirme
description: Azure 'daki sanal makine ölçek kümelerini özel görüntüyle güncelleştirmek için nasıl kullanılacağını öğrenin
keywords: ansible, azure, devops, bash, playbook, sanal makine, sanal makine ölçek kümesi, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b7d3053c09d2dcb667a4fc407035f4814f786932
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155854"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Öğretici: anormal kullanarak Azure sanal makine ölçek kümelerinin özel görüntüsünü güncelleştirme

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

VM dağıtıldıktan sonra, VM 'yi uygulamanızın ihtiyaç duyacağı yazılımla yapılandırırsınız. Her VM için bu yapılandırma görevini yerine özel bir görüntü oluşturabilirsiniz. Özel görüntü, yüklü herhangi bir yazılımı içeren mevcut bir sanal makinenin anlık görüntüsüdür. [Bir ölçek kümesi yapılandırdığınızda](./ansible-create-configure-vmss.md), bu ölçek kümesinin VM 'leri için kullanılacak görüntüyü belirtirsiniz. Özel bir görüntü kullanarak, her bir sanal makine örneği uygulamanız için aynı şekilde yapılandırılır. Bazen, ölçek kümesinin özel görüntüsünü güncelleştirmeniz gerekebilir. Bu görev, Bu öğreticinin odaklanmaktadır.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * HTTPD ile iki VM yapılandırma
> * Var olan bir VM 'den özel görüntü oluşturma
> * Görüntüden ölçek kümesi oluşturma
> * Özel görüntüyü güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>İki VM yapılandırma

Bu bölümdeki PlayBook kodu, her ikisinde de HTTPD yüklü iki sanal makine oluşturur. 

Her VM için `index.html` sayfası bir test dizesi görüntüler:

* İlk VM değeri görüntüler `Image A`
* İkinci VM `Image B` değeri görüntüler

Bu dize, her VM 'yi farklı yazılımlarla yapılandırmayı taklit etmek için tasarlanmıştır.

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) ve `create_vms.yml`kaydedin.
* `create_vms.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

`myrg` kaynak grubu adınızla değiştirerek, `ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

PlayBook 'un `debug` bölümleri nedeniyle `ansible-playbook` komutu her bir sanal makinenin IP adresini yazdırır. Bu IP adreslerini daha sonra kullanmak üzere kopyalayın.

![Sanal makine IP adresleri](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>İki VM 'ye bağlanma

Bu bölümde, her bir VM 'ye bağlanırsınız. Önceki bölümde belirtildiği gibi, dizeler `Image A` ve `Image B`, farklı yapılandırmalara sahip iki farklı VM 'ye sahip olacak şekilde taklit ediyor.

Önceki bölümden IP adreslerini kullanarak her iki VM 'ye bağlanın:

![Sanal makine A 'dan ekran görüntüsü](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![B sanal makinesinden ekran görüntüsü](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Her VM 'den görüntü oluşturma

Bu noktada, biraz farklı yapılandırmaya (`index.html` dosyaları) sahip iki sanal makine vardır.

Bu bölümdeki PlayBook kodu, her VM için özel bir görüntü oluşturur:

* `image_vmforimageA`-ana sayfasında `Image A` görüntüleyen VM için oluşturulan özel görüntü.
* `image_vmforimageB`-ana sayfasında `Image B` görüntüleyen VM için oluşturulan özel görüntü.

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) ve `capture-images.yml`kaydedin.
* `capture-images.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

`myrg` kaynak grubu adınızla değiştirerek, `ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Görüntü A kullanarak ölçek kümesi oluşturma

Bu bölümde, aşağıdaki Azure kaynaklarını yapılandırmak için bir PlayBook kullanılır:

* Genel IP adresi
* Yük dengeleyici
* `image_vmforimageA` başvuran ölçek kümesi

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) ve `create-vmss.yml`kaydedin.
* `create-vmss.yml` adlı yeni bir dosya oluşturun ve şu içerikleri içine kopyalayın: "

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

`myrg` kaynak grubu adınızla değiştirerek, `ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

PlayBook 'un `debug` bölümü nedeniyle `ansible-playbook` komutu, ölçek kümesinin IP adresini yazdıracaktır. Bu IP adresini daha sonra kullanmak üzere kopyalayın.

![Genel IP Adresi](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Ölçek kümesine bağlanma

Bu bölümde, ölçek kümesine bağlanırsınız. 

Önceki bölümden IP adresini kullanarak ölçek kümesine bağlanın.

Önceki bölümde belirtildiği gibi, dizeler `Image A` ve `Image B`, farklı yapılandırmalara sahip iki farklı VM 'ye sahip olacak şekilde taklit ediyor.

Ölçek kümesi, `image_vmforimageA`adlı özel görüntüye başvurur. Ana sayfası `Image A`görüntülenen VM 'den özel görüntü `image_vmforimageA` oluşturuldu.

Sonuç olarak, `Image A`görüntüleyen bir giriş sayfası görürsünüz:

![Ölçek kümesi ilk VM ile ilişkilendirilir.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Sonraki bölüme devam ederken tarayıcı pencerenizi açık bırakın.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Ölçek kümesi ve yükseltme örneklerinde özel görüntüyü değiştirme

Bu bölümdeki PlayBook kodu, ölçek kümesinin görüntüsünü `image_vmforimageA` `image_vmforimageB`olarak değiştirir. Ayrıca, ölçek kümesi tarafından dağıtılan tüm geçerli sanal makineler güncelleştirilir.

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) ve `update-vmss-image.yml`kaydedin.
* `update-vmss-image.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

`myrg` kaynak grubu adınızla değiştirerek, `ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Tarayıcıya dönün ve sayfayı yenileyin. 

Sanal makinenin temel alınan özel görüntüsünün güncelleştirildiğini görürsünüz.

![Ölçek kümesi ikinci VM ile ilişkili](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki kodu `cleanup.yml`olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
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
> [Azure üzerinde Ansible](/azure/ansible)
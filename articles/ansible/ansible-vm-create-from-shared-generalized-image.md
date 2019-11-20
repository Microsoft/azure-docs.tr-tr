---
title: Öğretici-Azure Paylaşılan görüntü galerisinden bir VM veya sanal makine ölçek kümesi oluşturma
description: Paylaşılan görüntü galerisinde genelleştirilmiş bir görüntüye göre VM veya sanal makine ölçek kümesi oluşturmak için nasıl kullanılacağını öğrenin.
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, sanal makine, sanal makine ölçek kümesi, paylaşılan görüntü Galerisi
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155829"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Öğretici: Azure Paylaşılan görüntü galerisinden bir VM veya sanal makine ölçek kümesi oluşturma

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Paylaşılan görüntü Galerisi](/azure/virtual-machines/windows/shared-image-galleries) , özel yönetilen görüntüleri kolayca yönetmenizi, paylaşmanızı ve düzenlemenizi sağlayan bir hizmettir. Bu özellik birçok görüntünün nerede tutulur ve paylaşıldığını senaryolar için faydalıdır. Özel görüntüler, abonelikler arasında ve Azure Active Directory kiracılar arasında paylaşılabilir. Daha hızlı dağıtım ölçeklendirmesi için görüntüler aynı zamanda birden çok bölgeye çoğaltılabilir.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Genelleştirilmiş bir VM ve özel görüntü oluşturma
> * Paylaşılan görüntü galerisi oluşturma
> * Paylaşılan görüntü ve görüntü sürümü oluşturma
> * Genelleştirilmiş görüntü kullanarak VM oluşturma
> * Genelleştirilmiş görüntü kullanarak bir sanal makine ölçek kümesi oluşturma
> * Paylaşılan görüntü galeriniz, görüntünüz ve sürümünüz hakkında bilgi alın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Örnek PlayBook 'ları alın

Tüm örnek PlayBook 'ları almanın iki yolu vardır:

- [SIG klasörünü indirin](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) ve yerel makinenize kaydedin.
- Her bölüm için yeni bir dosya oluşturun ve örnek PlayBook 'u kopyalayın.

`vars.yml` dosyası, bu öğretici için tüm örnek PlayBook 'lar tarafından kullanılan değişkenleri içerir. Benzersiz adlar ve değerler sağlamak için dosyayı düzenleyebilirsiniz.

İlk örnek PlayBook `00-prerequisites.yml`, bu öğreticiyi tamamlamaya yönelik gerekenleri oluşturur:
- Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir kapsayıcı olan bir kaynak grubu.
- Bir sanal ağ; alt ağ VM için genel IP adresi ve ağ arabirimi kartı.
- Genelleştirilmiş görüntü oluşturmak için kullanılan bir kaynak sanal makine.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 00-prerequisites.yml
```

[Azure Portal](https://portal.azure.com), yeni sanal makineyi ve oluşturduğunuz çeşitli kaynakları görmek için `vars.yml` belirttiğiniz kaynak grubunu kontrol edin.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>VM 'yi genelleştirin ve özel bir görüntü oluşturun

`01a-create-generalized-image.yml`bir sonraki PlayBook, önceki adımda oluşturulan kaynak VM 'yi genelleştirir ve bunu temel alan özel bir görüntü oluşturur.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Kaynak grubunuzu denetleyin ve `testimagea` göründüğünden emin olun.

## <a name="create-the-shared-image-gallery"></a>Paylaşılan görüntü galerisini oluşturma

Görüntü Galerisi, görüntüleri paylaşmak ve yönetmek için bir depodur. `02-create-shared-image-gallery.yml` örnek PlayBook kodu, kaynak grubunuzda paylaşılan bir görüntü Galerisi oluşturur.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Artık kaynak grubunuzda `myGallery`yeni bir galeri görürsünüz.

## <a name="create-a-shared-image-and-image-version"></a>Paylaşılan görüntü ve görüntü sürümü oluşturma

Sonraki PlayBook, `03a-create-shared-image-generalized.yml` bir görüntü tanımı ve bir görüntü sürümü oluşturur.

Görüntü tanımları, görüntü türü (Windows veya Linux), sürüm notları ve en düşük ve en yüksek bellek gereksinimlerini içerir. Görüntü sürümü görüntünün sürümüdür. Galeri, görüntü tanımı ve görüntü sürümü, mantıksal gruplardaki görüntüleri düzenlemenize yardımcı olur. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Kaynak grubunuz artık galeriniz için bir görüntü tanımına ve görüntü sürümüne sahip.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Genelleştirilmiş görüntüye göre VM oluşturma

Son olarak, önceki adımda oluşturduğunuz Genelleştirilmiş görüntüye göre bir VM oluşturmak için `04a-create-vm-using-generalized-image.yml` çalıştırın.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Genelleştirilmiş görüntü temelinde sanal makine ölçek kümeleri oluşturma

Genelleştirilmiş görüntüye göre bir sanal makine ölçek kümesi de oluşturabilirsiniz. Bunu yapmak için `05a-create-vmss-using-generalized-image.yml` çalıştırın.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Galeri hakkında bilgi alın

`06-get-info.yml`çalıştırarak Galeri, görüntü tanımı ve sürüm hakkında bilgi edinebilirsiniz.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Paylaşılan görüntüyü silme

Galeri kaynaklarını silmek için örnek PlayBook `07-delete-gallery.yml`bakın. Kaynakları ters sırada silin. Görüntü sürümünü silerek başlayın. Tüm görüntü sürümlerini sildikten sonra, görüntü tanımını silebilirsiniz. Tüm görüntü tanımlarını sildikten sonra, galeriyi silebilirsiniz.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Bu bölümdeki örnek PlayBook kodu şu şekilde kullanılır:

- Daha önce oluşturulan iki kaynak grubunu sil

Aşağıdaki playbook'u `cleanup.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- `{{ resource_group_name }}` yer tutucusunu kaynak grubunuzun adıyla değiştirin.
- Belirtilen iki kaynak grubu içindeki tüm kaynaklar silinecek.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure üzerinde Ansible](/azure/ansible/)
---
title: Python kullanarak Azure 'da Windows VM oluşturma ve yönetme
description: Azure 'da bir Windows sanal makinesi oluşturmak ve yönetmek için Python kullanmayı öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 1686e86600fb28165ae44e7ca6c0c406dfa26d72
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065591"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Python kullanarak Azure 'da Windows VM 'Leri oluşturma ve yönetme

[Azure sanal makinesi](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) birkaç destekleyici Azure kaynağı gerektirir. Bu makalede, Python kullanarak VM kaynaklarını oluşturma, yönetme ve silme konuları ele alınmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Visual Studio projesi oluşturma
> * Paketleri yükleme
> * Kimlik bilgileri oluştur
> * Kaynak oluşturma
> * Yönetim görevlerini gerçekleştirme
> * Kaynakları silme
> * Uygulamayı çalıştırma

Bu adımların uygulanması yaklaşık 20 dakika sürer.

## <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

1. Henüz yapmadıysanız, [Visual Studio 'yu](https://docs.microsoft.com/visualstudio/install/install-visual-studio)yükleyemezsiniz. Iş yükleri sayfasında **Python geliştirme** ' yi seçin ve ardından **yükler**' i tıklatın. Özette, **Python 3 64-bit (3.6.0)** ' in sizin için otomatik olarak seçili olduğunu görebilirsiniz. Visual Studio 'Yu önceden yüklediyseniz, Visual Studio başlatıcısı 'nı kullanarak Python iş yükünü ekleyebilirsiniz.
2. Visual Studio 'Yu yükleyip başlattıktan sonra **dosya** > **Yeni** > **projesi**' ne tıklayın.
3. **Şablonlar** > **Python** > **Python uygulaması**' na tıklayın, projenin adı Için *mypyıthonproject* girin, projenin konumunu seçin ve ardından **Tamam**' a tıklayın.

## <a name="install-packages"></a>Paketleri yükleme

1. Çözüm Gezgini, *Mypyıthonproject*altında, **Python ortamları**' na sağ tıklayın ve ardından **sanal ortam ekle**' yi seçin.
2. Sanal ortam ekle ekranında, varsayılan *env*adını kabul edin, temel yorumlayıcı için *Python 3,6 (64-bit)* ' in seçildiğinden emin olun ve ardından **Oluştur**' a tıklayın.
3. Oluşturduğunuz *env* ortamına sağ tıklayın, **Python paketini yükler**' e tıklayın, arama kutusuna *Azure* girin ve ardından ENTER tuşuna basın.

Çıkış penceresinde Azure paketlerinin başarıyla yüklendiğini görmeniz gerekir. 

## <a name="create-credentials"></a>Kimlik bilgileri oluştur

Bu adıma başlamadan önce bir [Active Directory Hizmet sorumlusuna](../../active-directory/develop/howto-create-service-principal-portal.md)sahip olduğunuzdan emin olun. Ayrıca, daha sonraki bir adımda ihtiyacınız olan uygulama KIMLIĞI, kimlik doğrulama anahtarı ve kiracı KIMLIĞINI de kaydetmeniz gerekir.

1. Oluşturulan *myPythonProject.py* dosyasını açın ve ardından uygulamanızın çalışmasını sağlamak için bu kodu ekleyin:

    ```python
    if __name__ == "__main__":
    ```

2. Gerekli kodu içeri aktarmak için, bu deyimleri. Kopyala dosyasının en üstüne ekleyin:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. . Kopyala dosyasında, kodda kullanılan ortak değerleri belirtmek için import deyimlerinden sonra değişkenler ekleyin:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    **Abonelik kimliğini** abonelik tanımlayıcın ile değiştirin.

4. İstek yapmak için ihtiyaç duyduğunuz Active Directory kimlik bilgilerini oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    **Uygulama kimliği**, **kimlik doğrulama anahtarı**ve **kiracı kimliği** ' ni, Azure Active Directory hizmet sorumlunuzu oluştururken daha önce topladığınız değerlerle değiştirin.

5. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Kaynak oluşturma
 
### <a name="initialize-management-clients"></a>Yönetim istemcilerini Başlat

Yönetim istemcilerinin Azure 'da Python SDK 'sını kullanarak kaynak oluşturup yönetmesi gerekir. Yönetim istemcilerini oluşturmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>VM ve destekleyici kaynakları oluşturma

Tüm kaynaklar bir [kaynak grubunda](../../azure-resource-manager/resource-group-overview.md)bulunmalıdır.

1. Bir kaynak grubu oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Kullanılabilirlik kümeleri](tutorial-availability-sets.md) , uygulamanız tarafından kullanılan sanal makineleri korumanıza daha kolay hale getirir.

1. Bir kullanılabilirlik kümesi oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

Sanal makineyle iletişim kurmak için [Genel BIR IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) gerekir.

1. Sanal makine için genel bir IP adresi oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Bir sanal makinenin bir [sanal ağın](../../virtual-network/virtual-networks-overview.md)alt ağında olması gerekir.

1. Bir sanal ağ oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Sanal ağa bir alt ağ eklemek için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Sanal bir makine, sanal ağ üzerinde iletişim kurmak için bir ağ arabirimine ihtiyaç duyuyor.

1. Bir ağ arabirimi oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Tüm destekleyici kaynakları oluşturduğunuza göre, bir sanal makine oluşturabilirsiniz.

1. Sanal makineyi oluşturmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Bu öğretici, Windows Server işletim sisteminin bir sürümünü çalıştıran bir sanal makine oluşturur. Diğer görüntüleri seçme hakkında daha fazla bilgi edinmek için bkz. [Windows PowerShell ve Azure CLI Ile Azure sanal makine görüntülerini gezinme ve seçme](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Yönetim görevlerini gerçekleştirme

Bir sanal makinenin yaşam döngüsü boyunca, sanal makineyi başlatmak, durdurmak veya silmek gibi yönetim görevleri gerçekleştirmek isteyebilirsiniz. Ayrıca, yinelenen veya karmaşık görevleri otomatikleştirmek için kod oluşturmak isteyebilirsiniz.

### <a name="get-information-about-the-vm"></a>VM hakkında bilgi edinme

1. Sanal makine hakkında bilgi almak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>VM’yi durdurma

Bir sanal makineyi durdurabilir ve tüm ayarlarını tutabilir, ancak ücretlendirmeye devam edebilir veya bir sanal makineyi durdurup serbest bırakabilirsiniz. Bir sanal makine serbest bırakıldığında, onunla ilişkili tüm kaynaklar da serbest bırakılır ve faturalandırılır.

1. Sanal makineyi ayırmayı kaldırmadan durdurmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Sanal makineyi serbest bırakmak istiyorsanız power_off çağrısı bu kodla değiştirin:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>VM’yi başlatma

1. Sanal makineyi başlatmak için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>VM 'yi yeniden boyutlandırma

Dağıtımın birçok yönü, sanal makineniz için bir boyuta karar verirken göz önünde bulundurulmalıdır. Daha fazla bilgi için bkz. [VM boyutları](sizes.md).

1. Sanal makinenin boyutunu değiştirmek için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>VM 'ye veri diski ekleme

Sanal makinelerde VHD olarak depolanan bir veya daha fazla [veri diski](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) olabilir.

1. Sanal makineye bir veri diski eklemek için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Kaynakları silme

Azure 'da kullanılan kaynaklar için ücretlendirildiğiniz için, artık gerekli olmayan kaynakları silmek her zaman iyi bir uygulamadır. Sanal makineleri ve tüm destekleyici kaynakları silmek istiyorsanız, tüm yapmanız gerekirse kaynak grubunu silmez.

1. Kaynak grubunu ve tüm kaynakları silmek için,. Kopyala dosyasındaki değişkenlerden sonra bu işlevi ekleyin:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Daha önce eklediğiniz işlevi çağırmak için, bu kodu. Kopyala dosyasının sonundaki **IF** ifadesinin altına ekleyin:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. *MyPythonProject.py*Kaydet.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

1. Konsol uygulamasını çalıştırmak için, Visual Studio 'da **Başlat** ' a tıklayın.

2. Her bir kaynağın durumu döndürüldüğünde **ENTER** tuşuna basın. Durum bilgilerinde, **başarılı** bir sağlama durumu görmeniz gerekir. Sanal makine oluşturulduktan sonra, oluşturduğunuz tüm kaynakları silme fırsatına sahip olursunuz. Kaynakları silmeye başlamak için **ENTER** tuşuna basmadan önce Azure Portal oluşturulmasını doğrulamanız birkaç dakika sürebilir. Azure portal açıksa, yeni kaynakları görmek için dikey pencereyi yenilemeniz gerekebilir.  

    Bu konsol uygulamasının başlangıçtan sonuna kadar tam olarak çalıştırılması yaklaşık beş dakika sürer. Tüm kaynaklar ve kaynak grubu silinmeden önce uygulamanın tamamlanmasının birkaç dakika sürebilir.


## <a name="next-steps"></a>Sonraki adımlar

- Dağıtım ile ilgili sorunlar varsa, bir sonraki adım [Azure portalındaki kaynak grubu dağıtımı sorunlarını giderme](../../resource-manager-troubleshoot-deployments-portal.md)’ye bakmak için olacaktır
- [Azure Python kitaplığı](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python) hakkında daha fazla bilgi edinin


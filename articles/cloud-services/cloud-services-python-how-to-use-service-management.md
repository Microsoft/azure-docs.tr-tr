---
title: Hizmet Yönetim API'si (Python)-özellik kılavuzunu kullanın
description: Python 'dan programlama yoluyla ortak hizmet yönetimi görevlerini nasıl gerçekleştireceğinizi öğrenin.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741409"
---
# <a name="use-service-management-from-python"></a>Python 'da hizmet yönetimi kullanma

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bu kılavuzda, Python 'dan programlı olarak ortak hizmet yönetimi görevlerini nasıl gerçekleştireceğiniz gösterilmektedir. [Python Için Azure SDK](https://github.com/Azure/azure-sdk-for-python) 'Daki **ServiceManagementService** sınıfı, [Azure Portal][management-portal]mevcut olan hizmet yönetimiyle ilgili işlevlerin büyük bölümüne programlı erişimi destekler. Bulut Hizmetleri, dağıtımlar, veri yönetimi hizmetleri ve sanal makineler oluşturmak, güncelleştirmek ve silmek için bu işlevi kullanabilirsiniz. Bu işlevsellik, hizmet yönetimine programlı erişim gerektiren uygulamalar oluşturmak için yararlı olabilir.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Hizmet yönetimi nedir?
Azure Hizmet Yönetim API'si, [Azure Portal][management-portal]üzerinden sunulan hizmet yönetimi işlevlerinin büyük bölümüne programlı erişim sağlar. Bulut hizmetlerinizi ve depolama hesaplarınızı yönetmek için Python için Azure SDK 'sını kullanabilirsiniz.

Hizmet Yönetim API'si kullanmak için [bir Azure hesabı oluşturmanız](https://azure.microsoft.com/pricing/free-trial/)gerekir.

## <a name="concepts"></a><a name="Concepts"> </a>Kavramlar
Python için Azure SDK, REST API olan [hizmet yönetim API'si][svc-mgmt-rest-api]sarmalanmış. Tüm API işlemleri TLS üzerinden gerçekleştirilir ve X. 509.440 v3 sertifikaları kullanılarak karşılıklı kimlik doğrulaması yapılır. Yönetim hizmetine Azure 'da çalışan bir hizmetin içinden erişilebilir. Ayrıca, HTTPS isteği gönderebilen ve HTTPS yanıtı alabilen herhangi bir uygulamadan Internet üzerinden doğrudan erişilebilir.

## <a name="installation"></a><a name="Installation"> </a>Yükleme
Bu makalede açıklanan tüm özellikler `azure-servicemanagement-legacy` pakette mevcuttur ve bu, PIP 'yi kullanarak yükleyebilirsiniz. Yükleme hakkında daha fazla bilgi için (örneğin, Python 'a yeni başladıysanız) bkz. [Python ve Azure SDK 'Yı yükleme](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Hizmet yönetimine Bağlan
Hizmet yönetimi uç noktasına bağlanmak için Azure abonelik KIMLIĞINIZ ve geçerli bir yönetim sertifikası gereklidir. Abonelik KIMLIĞINIZI [Azure Portal][management-portal]aracılığıyla elde edebilirsiniz.

> [!NOTE]
> Artık Windows üzerinde çalışırken OpenSSL ile oluşturulan sertifikaları kullanabilirsiniz. Python 2.7.4 veya üzeri gereklidir. . Pfx yerine OpenSSL kullanmanızı öneririz, çünkü. pfx sertifikalarının desteği gelecekte kaldırılabilir.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Windows/Mac/Linux (OpenSSL) üzerinde yönetim sertifikaları
[OpenSSL](https://www.openssl.org/) kullanarak yönetim sertifikanızı oluşturabilirsiniz. Biri sunucu (bir `.cer` dosya) ve bir istemci (bir dosya) için olmak üzere iki sertifika oluşturmanız gerekir `.pem` . Dosyayı oluşturmak için `.pem` şunu yürütün:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Sertifikayı oluşturmak için `.cer` şunu yürütün:

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Azure sertifikaları hakkında daha fazla bilgi için bkz. [azure Cloud Services sertifikalara genel bakış](cloud-services-certs-create.md). OpenSSL parametrelerinin ayrıntılı bir açıklaması için, belgelerine bakın [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

Bu dosyaları oluşturduktan sonra, `.cer` dosyayı Azure 'a yükleyin. [Azure Portal][management-portal], **Ayarlar** sekmesinde **karşıya yükle**' yi seçin. Dosyayı kaydettiğiniz yere göz önünde olursunuz `.pem` .

Abonelik KIMLIĞINIZ elde ettikten sonra bir sertifika oluşturun ve `.cer` dosyayı Azure 'a yükleyin, Azure Yönetim uç noktasına bağlanın. Abonelik KIMLIĞINI ve `.pem` dosyanın yolunu **ServiceManagementService**'e geçirerek bağlanın.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

Önceki örnekte, `sms` bir **ServiceManagementService** nesnesidir. **ServiceManagementService** sınıfı, Azure hizmetlerini yönetmek için kullanılan birincil sınıftır.

### <a name="management-certificates-on-windows-makecert"></a>Windows 'daki yönetim sertifikaları (MakeCert)
Kullanarak, makinenizde otomatik olarak imzalanan bir yönetim sertifikası oluşturabilirsiniz `makecert.exe` . **Yönetici** olarak bir **Visual Studio komut istemi** açın ve aşağıdaki komutu kullanarak *azurecercertificate* öğesini kullanmak istediğiniz sertifika adıyla değiştirin:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

Komut `.cer` dosyayı oluşturur ve **Kişisel** sertifika deposuna yüklenir. Daha fazla bilgi için bkz. [Azure Cloud Services sertifikalara genel bakış](cloud-services-certs-create.md).

Sertifikayı oluşturduktan sonra `.cer` dosyayı Azure 'a yükleyin. [Azure Portal][management-portal], **Ayarlar** sekmesinde **karşıya yükle**' yi seçin.

Abonelik KIMLIĞINIZ elde ettikten sonra bir sertifika oluşturun ve `.cer` dosyayı Azure 'a yükleyin, Azure Yönetim uç noktasına bağlanın. **Kişisel** sertifika deponuzdaki SERTIFIKANıN abonelik kimliğini ve konumunu **ServiceManagementService** 'e geçirerek bağlanın (yeniden, *azurecercertificate* değerini sertifikanızın adıyla değiştirin).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

Önceki örnekte, `sms` bir **ServiceManagementService** nesnesidir. **ServiceManagementService** sınıfı, Azure hizmetlerini yönetmek için kullanılan birincil sınıftır.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Kullanılabilir konumları listeleyin
Barındırma hizmetleri için kullanılabilen konumları listelemek için, **liste \_ konumları** yöntemini kullanın.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

Bir bulut hizmeti veya depolama hizmeti oluşturduğunuzda, geçerli bir konum sağlamanız gerekir. **Liste \_ konumları** yöntemi her zaman şu anda kullanılabilir konumların güncel bir listesini döndürür. Bu yazma itibariyle, kullanılabilir konumlar şunlardır:

* West Europe
* Kuzey Avrupa
* Güneydoğu Asya
* Doğu Asya
* Central US
* Orta Kuzey ABD
* Orta Güney ABD
* Batı ABD
* Doğu ABD
* Doğu Japonya
* Batı Japonya
* Güney Brezilya
* Doğu Avustralya
* Güneydoğu Avustralya

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Bulut hizmeti oluşturma
Bir uygulama oluşturup Azure 'da çalıştırdığınızda, koda ve yapılandırmaya birlikte bir Azure [bulut hizmeti][cloud service]denir. (Önceki Azure sürümlerindeki barındırılan bir *hizmet* olarak bilinirdi.) **\_ Barındırılan \_ hizmet oluşturma** yöntemini kullanarak yeni bir barındırılan hizmet oluşturabilirsiniz. Barındırılan bir hizmet adı (Azure 'da benzersiz olması gerekir), bir etiket (base64 olarak otomatik olarak kodlanır), bir açıklama ve bir konum sağlayarak hizmeti oluşturun.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

Şirket içinde barındırılan Hizmetleri **Listele \_ \_** yöntemiyle aboneliğiniz için tüm barındırılan Hizmetleri listeleyebilirsiniz.

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Belirli bir barındırılan hizmet hakkında bilgi almak için barındırılan hizmet adını **\_ barındırılan \_ hizmet \_ özelliklerini al** metoduna geçirin.

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

Bir bulut hizmeti oluşturduktan sonra, **\_ dağıtım oluşturma** yöntemiyle kodunuzu hizmete dağıtın.

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Bulut hizmetini silme
Hizmet adını **\_ barındırılan \_ hizmet** yöntemine geçirerek bir bulut hizmetini silebilirsiniz.

```python
sms.delete_hosted_service('myhostedservice')
```

Bir hizmeti silebilmeniz için önce hizmet için tüm dağıtımların silinmesi gerekir. Daha fazla bilgi için bkz. [bir dağıtımı silme](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Bir dağıtımı silme
Bir dağıtımı silmek için, **\_ dağıtımı Sil** yöntemini kullanın. Aşağıdaki örnek adlı bir dağıtımın nasıl silineceğini göstermektedir `v1` :

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Depolama hizmeti oluşturma
[Depolama hizmeti](../storage/common/storage-account-create.md) , Azure [BLOB 'ları](../storage/blobs/storage-quickstart-blobs-python.md), [tabloları](../cosmos-db/table-storage-how-to-use-python.md)ve [kuyrukları](../storage/queues/storage-python-how-to-use-queue-storage.md)için erişmenizi sağlar. Bir depolama hizmeti oluşturmak için, hizmet için bir ad (3 ila 24 küçük harf ve Azure içinde benzersiz) gerekir. Ayrıca bir açıklama, etiket (en fazla 100 karakter, Base64 olarak kodlanmış) ve bir konum gerekir. Aşağıdaki örnek, bir konum belirterek depolama hizmeti oluşturmayı göstermektedir:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Önceki örnekte **, depolama hesabı oluşturma işleminin durumu \_ \_** , **\_ depolama \_ hesabı oluşturma** işlemi **\_ \_ durum** metoduna döndürülen sonuç alınarak alınabilir. 

Depolama hesaplarınızı ve bunların özelliklerini **liste \_ depolama \_ hesapları** yöntemiyle listeleyebilirsiniz.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Depolama hizmetini silme
Depolama hizmetini silmek için depolama hizmeti adını **\_ depolama \_ hesabı silme** metoduna geçirin. Depolama hizmeti silindiğinde, hizmette depolanan tüm veriler (Bloblar, tablolar ve kuyruklar) silinir.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Kullanılabilir işletim sistemlerini listeleme
Barındırma hizmetleri için kullanılabilen işletim sistemlerini listelemek için, **\_ işletim \_ sistemlerini Listele** yöntemini kullanın.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

Alternatif olarak, işletim sistemlerini ailesine göre gruplandıran **\_ işletim \_ sistemi \_ aileleri** yöntemini de kullanabilirsiniz.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Bir işletim sistemi görüntüsü oluşturma
Görüntü deposuna bir işletim sistemi görüntüsü eklemek için, **\_ işletim sistemi \_ görüntüsü Ekle** yöntemini kullanın.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Kullanılabilir işletim sistemi görüntülerini listelemek için **\_ OS \_ görüntülerini Listele** yöntemini kullanın. Tüm platform görüntülerini ve Kullanıcı görüntülerini içerir.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Bir işletim sistemi görüntüsünü silme
Bir Kullanıcı görüntüsünü silmek için **\_ OS \_ görüntüsünü silme** yöntemini kullanın.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Sanal makine oluşturma
Bir sanal makine oluşturmak için önce bir [bulut hizmeti](#CreateCloudService)oluşturmanız gerekir. Ardından sanal makine dağıtımı oluşturma yöntemini kullanarak sanal makine **dağıtımını \_ \_ \_** oluşturun.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Sanal makineyi sil
Bir sanal makineyi silmek için önce dağıtımı **Sil \_** yöntemini kullanarak dağıtımı silmelisiniz.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

Bulut hizmeti daha sonra **\_ barındırılan \_ hizmet silme** yöntemi kullanılarak silinebilir.

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Yakalanan bir sanal makine görüntüsünden sanal makine oluşturma
Bir VM görüntüsünü yakalamak için, önce **yakalama \_ VM \_ görüntüsü** yöntemini çağırmanız gerekir.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Görüntüyü başarıyla yakaladığınızdan emin olmak için **\_ VM \_ görüntülerini Listele** API 'sini kullanın. Resminizin sonuçlarda görüntülendiğinden emin olun.

```python
images = sms.list_vm_images()
```

Son olarak, yakalanan görüntüyü kullanarak sanal makineyi oluşturmak için, **\_ sanal \_ makine \_ dağıtımı oluşturma** yöntemini daha önce olduğu gibi kullanın, ancak bu zaman bunun yerine vm_image_name geçer.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Klasik dağıtım modelinde Linux sanal makinesini yakalama hakkında daha fazla bilgi edinmek için bkz. [bir Linux sanal makinesi yakalama](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic).

Klasik dağıtım modelinde bir Windows sanal makinesini yakalama hakkında daha fazla bilgi edinmek için bkz. [Windows sanal makinesi yakalama](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic).

## <a name="next-steps"></a><a name="What's Next"> </a>Sonraki adımlar
Hizmet yönetiminin temellerini öğrendiğinize göre, [Azure Python SDK 'Nın tüm API başvuru belgelerine](https://azure-sdk-for-python.readthedocs.org/) erişebilir ve Python uygulamanızı yönetmek için karmaşık görevleri kolayca gerçekleştirebilirsiniz.

Daha fazla bilgi için bkz. [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/
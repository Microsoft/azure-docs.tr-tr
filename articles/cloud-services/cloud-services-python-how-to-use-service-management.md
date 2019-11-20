---
title: Hizmet Yönetim API'si (Python)-özellik kılavuzunu kullanın
description: Python 'dan programlama yoluyla ortak hizmet yönetimi görevlerini nasıl gerçekleştireceğinizi öğrenin.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 50501413a63921a9a34be1c04ed259990922b686
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141476"
---
# <a name="use-service-management-from-python"></a>Python 'da hizmet yönetimi kullanma
Bu kılavuzda, Python 'dan programlı olarak ortak hizmet yönetimi görevlerini nasıl gerçekleştireceğiniz gösterilmektedir. [Python Için Azure SDK](https://github.com/Azure/azure-sdk-for-python) 'Daki **ServiceManagementService** sınıfı, [Azure Portal][management-portal]mevcut olan hizmet yönetimiyle ilgili işlevlerin büyük bölümüne programlı erişimi destekler. Bulut Hizmetleri, dağıtımlar, veri yönetimi hizmetleri ve sanal makineler oluşturmak, güncelleştirmek ve silmek için bu işlevi kullanabilirsiniz. Bu işlevsellik, hizmet yönetimine programlı erişim gerektiren uygulamalar oluşturmak için yararlı olabilir.

## <a name="WhatIs"> </a>Hizmet yönetimi nedir?
Azure Hizmet Yönetim API'si, [Azure Portal][management-portal]üzerinden sunulan hizmet yönetimi işlevlerinin büyük bölümüne programlı erişim sağlar. Bulut hizmetlerinizi ve depolama hesaplarınızı yönetmek için Python için Azure SDK 'sını kullanabilirsiniz.

Hizmet Yönetim API'si kullanmak için [bir Azure hesabı oluşturmanız](https://azure.microsoft.com/pricing/free-trial/)gerekir.

## <a name="Concepts"> </a>Kavramlar
Python için Azure SDK, REST API olan [hizmet yönetim API'si][svc-mgmt-rest-api]sarmalanmış. Tüm API işlemleri SSL üzerinden gerçekleştirilir ve X. 509.440 v3 sertifikaları kullanılarak karşılıklı kimlik doğrulaması yapılır. Yönetim hizmetine Azure 'da çalışan bir hizmetin içinden erişilebilir. Ayrıca, HTTPS isteği gönderebilen ve HTTPS yanıtı alabilen herhangi bir uygulamadan Internet üzerinden doğrudan erişilebilir.

## <a name="Installation"> </a>Yükleme
Bu makalede açıklanan tüm özellikler `azure-servicemanagement-legacy` pakette mevcuttur ve bu, PIP 'yi kullanarak yükleyebilirsiniz. Yükleme hakkında daha fazla bilgi için (örneğin, Python 'a yeni başladıysanız) bkz. [Python ve Azure SDK 'Yı yükleme](/azure/python/python-sdk-azure-install).

## <a name="Connect"> </a>Hizmet yönetimine Bağlan
Hizmet yönetimi uç noktasına bağlanmak için Azure abonelik KIMLIĞINIZ ve geçerli bir yönetim sertifikası gereklidir. Abonelik KIMLIĞINIZI [Azure Portal][management-portal]aracılığıyla elde edebilirsiniz.

> [!NOTE]
> Artık Windows üzerinde çalışırken OpenSSL ile oluşturulan sertifikaları kullanabilirsiniz. Python 2.7.4 veya üzeri gereklidir. . Pfx yerine OpenSSL kullanmanızı öneririz, çünkü. pfx sertifikalarının desteği gelecekte kaldırılabilir.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Windows/Mac/Linux (OpenSSL) üzerinde yönetim sertifikaları
[OpenSSL](https://www.openssl.org/) kullanarak yönetim sertifikanızı oluşturabilirsiniz. Biri sunucu (bir `.cer` dosya) ve bir istemci (bir `.pem` dosya) için olmak üzere iki sertifika oluşturmanız gerekir. `.pem` Dosyayı oluşturmak için şunu yürütün:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

`.cer` Sertifikayı oluşturmak için şunu yürütün:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Azure sertifikaları hakkında daha fazla bilgi için bkz. [azure Cloud Services sertifikalara genel bakış](cloud-services-certs-create.md). OpenSSL parametrelerinin ayrıntılı bir açıklaması için, belgelerine [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)bakın.

Bu dosyaları oluşturduktan sonra, `.cer` dosyayı Azure 'a yükleyin. [Azure Portal][management-portal], **Ayarlar** sekmesinde **karşıya yükle**' yi seçin. `.pem` Dosyayı kaydettiğiniz yere göz önünde olursunuz.

Abonelik kimliğiniz elde ettikten sonra bir sertifika oluşturun ve `.cer` dosyayı Azure 'a yükleyin, Azure Yönetim uç noktasına bağlanın. Abonelik kimliğini ve `.pem` dosyanın yolunu **ServiceManagementService**'e geçirerek bağlanın.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Önceki örnekte, `sms` bir **ServiceManagementService** nesnesidir. **ServiceManagementService** sınıfı, Azure hizmetlerini yönetmek için kullanılan birincil sınıftır.

### <a name="management-certificates-on-windows-makecert"></a>Windows 'daki yönetim sertifikaları (MakeCert)
Kullanarak `makecert.exe`, makinenizde otomatik olarak imzalanan bir yönetim sertifikası oluşturabilirsiniz. **Yönetici** olarak bir **Visual Studio komut istemi** açın ve aşağıdaki komutu kullanarak *azurecercertificate* öğesini kullanmak istediğiniz sertifika adıyla değiştirin:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Komut `.cer` dosyayı oluşturur ve **Kişisel** sertifika deposuna yüklenir. Daha fazla bilgi için bkz. [Azure Cloud Services sertifikalara genel bakış](cloud-services-certs-create.md).

Sertifikayı oluşturduktan sonra `.cer` dosyayı Azure 'a yükleyin. [Azure Portal][management-portal], **Ayarlar** sekmesinde **karşıya yükle**' yi seçin.

Abonelik kimliğiniz elde ettikten sonra bir sertifika oluşturun ve `.cer` dosyayı Azure 'a yükleyin, Azure Yönetim uç noktasına bağlanın. **Kişisel** sertifika deponuzdaki SERTIFIKANıN abonelik kimliğini ve konumunu **ServiceManagementService** 'e geçirerek bağlanın (yeniden, *azurecercertificate* değerini sertifikanızın adıyla değiştirin).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Önceki örnekte, `sms` bir **ServiceManagementService** nesnesidir. **ServiceManagementService** sınıfı, Azure hizmetlerini yönetmek için kullanılan birincil sınıftır.

## <a name="ListAvailableLocations"> </a>Kullanılabilir konumları listeleyin
Barındırma hizmetleri için kullanılabilen konumları listelemek için, **liste\_konumları** yöntemini kullanın.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Bir bulut hizmeti veya depolama hizmeti oluşturduğunuzda, geçerli bir konum sağlamanız gerekir. **Liste\_konumları** yöntemi her zaman şu anda kullanılabilir konumların güncel bir listesini döndürür. Bu yazma itibariyle, kullanılabilir konumlar şunlardır:

* Batı Avrupa
* Kuzey Avrupa
* Güneydoğu Asya
* Doğu Asya
* Orta ABD
* Orta Kuzey ABD
* Orta Güney ABD
* Batı ABD
* East US
* Japonya Doğu
* Japonya Batı
* Güney Brezilya
* Avustralya Doğu
* Avustralya Güneydoğu

## <a name="CreateCloudService"> </a>Bulut hizmeti oluşturma
Bir uygulama oluşturup Azure 'da çalıştırdığınızda, koda ve yapılandırmaya birlikte bir Azure [bulut hizmeti][cloud service]denir. (Önceki Azure sürümlerindeki barındırılan bir *hizmet* olarak bilinirdi.) **\_Barındırılanhizmet\_oluşturma** yöntemini kullanarak yeni bir barındırılan hizmet oluşturabilirsiniz. Barındırılan bir hizmet adı (Azure 'da benzersiz olması gerekir), bir etiket (base64 olarak otomatik olarak kodlanır), bir açıklama ve bir konum sağlayarak hizmeti oluşturun.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Şirket içinde barındırılan Hizmetleri **Listele\_yöntemiyle aboneliğiniz için tüm barındırılan Hizmetleri listeleyebilirsiniz.\_**

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Belirli bir barındırılan hizmet hakkında bilgi almak için barındırılan hizmet adını **barındırılan\_\_\_hizmet özelliklerini al** metoduna geçirin.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Bir bulut hizmeti oluşturduktan sonra, **dağıtım oluşturma\_** yöntemiyle kodunuzu hizmete dağıtın.

## <a name="DeleteCloudService"> </a>Bulut hizmetini silme
Hizmet adını **\_barındırılan\_hizmet** yöntemine geçirerek bir bulut hizmetini silebilirsiniz.

    sms.delete_hosted_service('myhostedservice')

Bir hizmeti silebilmeniz için önce hizmet için tüm dağıtımların silinmesi gerekir. Daha fazla bilgi için bkz. [bir dağıtımı silme](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Bir dağıtımı silme
Bir dağıtımı silmek için, **dağıtımı Sil\_** yöntemini kullanın. Aşağıdaki örnek adlı `v1`bir dağıtımın nasıl silineceğini göstermektedir:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Depolama hizmeti oluşturma
[Depolama hizmeti](../storage/common/storage-create-storage-account.md) , Azure [BLOB 'ları](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabloları](../cosmos-db/table-storage-how-to-use-python.md)ve [kuyrukları](../storage/queues/storage-python-how-to-use-queue-storage.md)için erişmenizi sağlar. Bir depolama hizmeti oluşturmak için, hizmet için bir ad (3 ila 24 küçük harf ve Azure içinde benzersiz) gerekir. Ayrıca bir açıklama, etiket (en fazla 100 karakter, Base64 olarak kodlanmış) ve bir konum gerekir. Aşağıdaki örnek, bir konum belirterek depolama hizmeti oluşturmayı göstermektedir:

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

Yukarıdaki örnekte, depolama hesabı oluştur işleminin durumu **\_\_** , Get **\_\_** **'edepolamahesabıtarafındandöndürülensonuçgeçirilmesiylealınabilir.\_işlemdurumu\_** yöntemi. 

Depolama hesaplarınızı ve bunların özelliklerini **\_liste depolama\_hesapları** yöntemiyle listeleyebilirsiniz.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Depolama hizmetini silme
Depolama hizmetini silmek için depolama hizmeti adını **\_depolama\_hesabı silme** metoduna geçirin. Depolama hizmeti silindiğinde, hizmette depolanan tüm veriler (Bloblar, tablolar ve kuyruklar) silinir.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Kullanılabilir işletim sistemlerini listeleme
Barındırma hizmetleri için kullanılabilen işletim sistemlerini listelemek için, **\_işletim\_sistemlerini Listele** yöntemini kullanın.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternatif olarak, işletim sistemlerini ailesine **göre\_gruplandıran\_işletim\_sistemi aileleri** yöntemini de kullanabilirsiniz.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Bir işletim sistemi görüntüsü oluşturma
Görüntü deposuna bir işletim sistemi görüntüsü eklemek için, **\_işletim\_sistemi görüntüsü Ekle** yöntemini kullanın.

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

Kullanılabilir işletim sistemi görüntülerini listelemek için **\_OS\_görüntülerini Listele** yöntemini kullanın. Tüm platform görüntülerini ve Kullanıcı görüntülerini içerir.

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

## <a name="DeleteVMImage"> </a>Bir işletim sistemi görüntüsünü silme
Bir Kullanıcı görüntüsünü silmek için **\_OS\_görüntüsünü silme** yöntemini kullanın.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Sanal makine oluşturma
Bir sanal makine oluşturmak için önce bir [bulut hizmeti](#CreateCloudService)oluşturmanız gerekir. Ardından sanal makine dağıtımı **oluşturma\_\_\_** yöntemini kullanarak sanal makine dağıtımını oluşturun.

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

## <a name="DeleteVM"> </a>Bir sanal makineyi silme
Bir sanal makineyi silmek için önce dağıtımı **Sil\_** yöntemini kullanarak dağıtımı silmelisiniz.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Bulut hizmeti daha sonra **\_barındırılan\_hizmet silme** yöntemi kullanılarak silinebilir.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Yakalanan bir sanal makine görüntüsünden sanal makine oluşturma
Bir VM görüntüsünü yakalamak için, önce **\_yakalama VM\_görüntüsü** yöntemini çağırmanız gerekir.

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

Görüntüyü başarıyla yakaladığınızdan emin olmak için **\_VM\_görüntülerini Listele** API 'sini kullanın. Resminizin sonuçlarda görüntülendiğinden emin olun.

    images = sms.list_vm_images()

Son olarak, yakalanan görüntüyü kullanarak sanal makineyi oluşturmak için, **sanal\_makine\_dağıtımı oluşturma\_** yöntemini daha önce olduğu gibi kullanın, ancak bu zaman bunun yerine vm_image_name geçer.

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

Klasik dağıtım modelinde Linux sanal makinesini yakalama hakkında daha fazla bilgi edinmek için bkz. [bir Linux sanal makinesi yakalama](../virtual-machines/linux/classic/capture-image-classic.md).

Klasik dağıtım modelinde bir Windows sanal makinesini yakalama hakkında daha fazla bilgi edinmek için bkz. [Windows sanal makinesi yakalama](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Sonraki adımlar
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
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/

---
title: 'Sorun giderme hatası: Azure İşlevleri Çalışma Zamanı ulaşılamıyor'
description: Geçersiz bir depolama hesabında sorun gidermeyi öğrenin.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 392882fc2f0394e61aee973a20479d8f1fa9bc92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606982"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Sorun giderme hatası: "Azure İşlevleri Çalışma Zamanı erişilebilir değil"

Bu makale, Azure portal görüntülenen aşağıdaki hata dizesinin sorunlarını gidermenize yardımcı olur:

> "Hata: Azure İşlevleri Çalışma Zamanı ulaşılamaz. Depolama yapılandırmasıyla ilgili ayrıntılar için buraya tıklayın. "

Bu sorun, Işlevler çalışma zamanının başlayamayacağı zaman oluşur. Bunun en yaygın nedeni, işlev uygulamasının depolama hesabına erişiminin kaybedilme nedenidir. Daha fazla bilgi için bkz. [depolama hesabı gereksinimleri](storage-considerations.md#storage-account-requirements).

Bu makalenin geri kalanında, her bir durumu belirleme ve çözme dahil olmak üzere bu hatanın belirli nedenlerine ilişkin sorunları gidermenize yardımcı olur.

## <a name="storage-account-was-deleted"></a>Depolama hesabı silindi

Her işlev uygulamasının çalışması için bir depolama hesabının olması gerekir. Bu hesap silinirse, işlevleriniz çalışmaz.

Uygulama ayarlarınızda depolama hesabı adınızı arayarak başlayın. `AzureWebJobsStorage`Ya da `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bir bağlantı dizesinin parçası olarak depolama hesabınızın adını içerir. Daha fazla bilgi için bkz. [Azure işlevleri Için uygulama ayarları başvurusu](./functions-app-settings.md#azurewebjobsstorage).

Hala mevcut olup olmadığını görmek için Azure portal depolama hesabınızı arayın. Silinmişse, depolama hesabını yeniden oluşturun ve depolama bağlantı dizelerinizi değiştirin. İşlev kodunuz kaybolur ve yeniden dağıtmanız gerekir.

## <a name="storage-account-application-settings-were-deleted"></a>Depolama hesabı uygulama ayarları silindi

Önceki adımda, bir depolama hesabı bağlantı dizesi bulamazsanız muhtemelen silinmiş veya üzerine yazılmıştı. Uygulama ayarlarının silinmesi, dağıtım yuvaları veya Azure Resource Manager betikleri kullanırken uygulama ayarlarını ayarlamak için genellikle gerçekleşir.

### <a name="required-application-settings"></a>Gerekli uygulama ayarları

* Gerekli:
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Premium plan işlevleri için gereklidir:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Daha fazla bilgi için bkz. [Azure işlevleri Için uygulama ayarları başvurusu](./functions-app-settings.md).

### <a name="guidance"></a>Rehber

* Bu ayarlardan herhangi biri için **yuva ayarını** denetleme. Dağıtım yuvalarını takas ederseniz, işlev uygulaması kesilir.
* Bu ayarları otomatik dağıtımların parçası olarak değiştirmeyin.
* Bu ayarlar, oluşturma sırasında sağlanmalı ve geçerli olmalıdır. Bu ayarları içermeyen bir otomatik dağıtım, ayarlar daha sonra eklense bile çalıştırmayacak bir işlev uygulamasına neden olur.

## <a name="storage-account-credentials-are-invalid"></a>Depolama hesabı kimlik bilgileri geçersiz

Depolama anahtarlarını yeniden oluşturursanız, daha önce tartışılan depolama hesabı bağlantı dizeleri güncelleştirilmeleri gerekir. Depolama anahtarı yönetimi hakkında daha fazla bilgi için bkz. [Azure Storage hesabı oluşturma](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>Depolama hesabına erişilemiyor

İşlev uygulamanız depolama hesabına erişebilmelidir. Bir işlev uygulamasının bir depolama hesabına erişimini engelleyen yaygın sorunlar şunlardır:

* İşlev uygulaması, depolama hesabından gelen ve giden trafiğe izin vermek için doğru ağ kuralları olmadan App Service Ortamı (Ao) uygulamasına dağıtılır.

* Depolama hesabı güvenlik duvarı etkinleştirilir ve işlevlere ve işlevlerine giden trafiğe izin verecek şekilde yapılandırılmamıştır. Daha fazla bilgi için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
* `allowSharedKeyAccess`Ayarın varsayılan değeri olan olarak ayarlandığını doğrulayın `true` . Daha fazla bilgi için bkz. [Azure depolama hesabı Için paylaşılan anahtar yetkilendirmesini engelleme](https://docs.microsoft.com/azure/storage/common/shared-key-authorization-prevent?tabs=portal#verify-that-shared-key-access-is-not-allowed). 

## <a name="daily-execution-quota-is-full"></a>Günlük yürütme kotası dolu

Günlük yürütme kotayı yapılandırdıysanız, işlev uygulamanız geçici olarak devre dışı bırakılır ve bu da Portal denetimlerinin çoğunun kullanılamaz hale gelmesine neden olur. 

[Azure Portal](https://portal.azure.com)kotayı doğrulamak için, işlev uygulamanızda **platform özellikleri**  >  **işlev uygulaması ayarları** ' nı seçin. Ayarladığınız **günlük kullanım kotasının** üzerinde çalışıyorsanız, aşağıdaki ileti görüntülenir:

  > "İşlev Uygulaması günlük kullanım kotasına ulaştı ve sonraki 24 saatlik zaman dilimine kadar durduruldu."

Bu sorunu çözmek için günlük kotasını kaldırın veya arttırın ve sonra uygulamanızı yeniden başlatın. Aksi takdirde, uygulamanızın yürütülmesi bir sonraki güne kadar engellenir.

## <a name="app-is-behind-a-firewall"></a>Uygulama bir güvenlik duvarının arkasında

İşlev uygulamanıza aşağıdaki nedenlerden biri için ulaşılamıyor olabilir:

* İşlev uygulamanız, [dahili olarak yük dengeli bir App Service ortamı](../app-service/environment/create-ilb-ase.md) barındırılır ve gelen internet trafiğini engelleyecek şekilde yapılandırılmıştır.

* İşlev uygulamanızda internet erişimini engelleyecek şekilde yapılandırılmış [gelen IP kısıtlamaları](functions-networking-options.md#inbound-access-restrictions) vardır. 

Azure portal işlevler listesini getirmek için doğrudan çalışan uygulamaya çağrı yapar ve kudu uç noktasına HTTP çağrıları yapar. **Platform özellikleri** sekmesi altındaki platform düzeyi ayarları hala kullanılabilir.

ATıCı yapılandırmanızı doğrulamak için:
1. ATıCı 'in bulunduğu alt ağın ağ güvenlik grubuna (NSG) gidin.
1. Uygulamaya eriştiğiniz bilgisayarın genel IP 'sinden gelen trafiğe izin vermek için gelen kurallarını doğrulayın. 
   
Portalı, uygulamanızı çalıştıran sanal ağa veya sanal ağınızda çalışan bir sanal makineye bağlı bir bilgisayardan da kullanabilirsiniz. 

Gelen kuralı yapılandırması hakkında daha fazla bilgi için, [bir App Service ortamı Için ağ oluşturma konuları](../app-service/environment/network-info.md#network-security-groups)"ağ güvenlik grupları" bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

İşlev uygulamalarınızı izleme hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Azure İşlevlerini İzleme](functions-monitoring.md)

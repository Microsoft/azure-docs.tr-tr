---
title: Sorun giderme Azure İşlevleri Çalışma Zamanı ulaşılamaz.
description: Geçersiz bir depolama hesabında sorun gidermeyi öğrenin.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: d5959acc7719e2b02d529bca8261bc09d5b93634
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085321"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>"İşlevler çalışma zamanına ulaşılamıyor" sorununu giderme


## <a name="error-text"></a>Hata metni
Bu belge, Işlevler portalında görüntülendiğinde aşağıdaki hatayla ilgili sorunları gidermeye yöneliktir.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Özet
Azure İşlevleri Çalışma Zamanı başlayamediğinde bu sorun oluşur. Bu hatanın oluşma en yaygın nedeni, işlev uygulamasının depolama hesabına erişimi kaybetmesi durumunda meydana gelir. [Depolama hesabı gereksinimleri hakkında buradan daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Sorun giderme
En yaygın dört hata durumuna, nasıl tanımlanacağına ve her bir durumda nasıl çözümleneceğini adım adım inceleyeceğiz.

1. Depolama hesabı silindi
1. Depolama hesabı uygulama ayarları silindi
1. Depolama hesabı kimlik bilgileri geçersiz
1. Depolama hesabına erişilemiyor
1. Günlük yürütme kotası dolu

## <a name="storage-account-deleted"></a>Depolama hesabı silindi

Her işlev uygulamasının çalışması için bir depolama hesabının olması gerekir. Bu hesap silinirse Işleviniz çalışmaz.

### <a name="how-to-find-your-storage-account"></a>Depolama hesabınızı bulma

Uygulama ayarlarınızda depolama hesabı adınızı arayarak başlayın. `AzureWebJobsStorage` Ya`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` da, bir bağlantı dizesinde sarmalanmış depolama hesabınızın adını içerir. [Burada uygulama ayarı başvurusu](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage) ' na daha fazla bilgi okuyun

Hala mevcut olup olmadığını görmek için Azure portal depolama hesabınızı arayın. Silinmişse, bir depolama hesabı oluşturmanız ve depolama bağlantı Dizelerinizin yerine getirmeniz gerekir. İşlev kodunuz kaybolur ve yeniden dağıtmanız gerekir.

## <a name="storage-account-application-settings-deleted"></a>Depolama hesabı uygulama ayarları silindi

Önceki adımda, büyük olasılıkla silinmiş veya üzerine yazılmış bir depolama hesabı bağlantı dizeniz yoksa. Uygulama ayarlarını yapmak için dağıtım yuvaları veya Azure Resource Manager betikleri kullanılırken uygulama ayarlarının silinmesi genellikle yapılır.

### <a name="required-application-settings"></a>Gerekli uygulama ayarları

* Gerekli
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Tüketim planı Işlevleri için gereklidir
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Bu uygulama ayarları hakkında buradan okuyun](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Rehber

* Bu ayarlardan herhangi biri için "yuva ayarı" denetimini kaldırmayın. Dağıtım yuvalarını değiştirdiğinizde Işlev kesilir.
* Bu ayarları otomatik dağıtımların parçası olarak değiştirmeyin.
* Bu ayarlar, oluşturma sırasında sağlanmalı ve geçerli olmalıdır. Bu ayarları içermeyen bir otomatik dağıtım, ayarlar olgusuna sonra eklense bile işlevsel olmayan bir uygulamaya neden olur.

## <a name="storage-account-credentials-invalid"></a>Depolama hesabı kimlik bilgileri geçersiz

Depolama anahtarlarını yeniden oluşturursanız yukarıdaki depolama hesabı bağlantı dizelerinin güncellenmesi gerekir. [Depolama anahtarı yönetimi hakkında buradan daha fazla bilgi edinin](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Depolama hesabına erişilemiyor

İşlev Uygulaması depolama hesabına erişebilmelidir. Bir depolama hesabına erişim Işlevi engelleyen yaygın sorunlar şunlardır:

* Depolama hesabından gelen ve giden trafiğe izin vermek için doğru ağ kuralları olmadan App Service ortamlara dağıtılan işlev uygulamaları
* Depolama hesabı güvenlik duvarı etkinleştirilir ve IŞLEVLERE ve Işlevlerine giden trafiğe izin verecek şekilde yapılandırılmamıştır. [Depolama hesabı güvenlik duvarı yapılandırması hakkında buradan daha fazla bilgi edinin](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Günlük yürütme kotası dolu

Günlük yürütme kotayı yapılandırdıysanız, İşlev Uygulaması geçici olarak devre dışı bırakılır ve Portal denetimlerinin birçoğu kullanılamaz hale gelir. 

* Doğrulamak için, portalda açık platform özellikleri > İşlev Uygulaması ayarları ' na bakın. Kotayı daha fazla olursa aşağıdaki iletiyi görürsünüz
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Sorunu çözmek için kotayı kaldırın ve uygulamanızı yeniden başlatın.

## <a name="next-steps"></a>Sonraki Adımlar

Artık İşlev Uygulaması ve çalışır duruma getirilene kadar hızlı başlangıç ve geliştirici başvurularımıza bakarak yeniden çalışmaya başlayın!

* [İlk Azure İşlevinizi oluşturma](functions-create-first-azure-function.md)  
  Hemen başlayın ve Azure İşlevleri hızlı başlangıcını kullanarak ilk işlevinizi oluşturun. 
* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)  
  Azure İşlevleri çalışma zamanı hakkında daha teknik bilgiler ve işlevlerin kodlanması ve tetikleyicilerin ve bağlamaların tanımlanması hakkında bir başvuru sağlar.
* [Azure İşlevlerini test etme](functions-test-a-function.md)  
  İşlevlerinizi test etmek için çeşitli araçları ve teknikleri açıklar.
* [Azure İşlevlerini ölçeklendirme](functions-scale.md)  
  Tüketim barındırma planı dahil olmak üzere, Azure İşlevlerinde kullanılabilen hizmet planlarını ve doğru planın nasıl seçileceğini açıklar. 
* [Azure Uygulama Hizmeti hakkında daha fazla bilgi edinin](../app-service/overview.md)  
  Azure İşlevleri; dağıtımlar, ortam değişkenleri ve tanılama gibi temel işlevler için Azure App Service’i kullanır. 

---
title: Azure IoT Edge modülü teknik varlıkları oluşturma | Azure Marketi
description: IoT Edge modülü için teknik varlıklar oluşturun.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 57bc2f789836a7d3453004cdacc59029c4b24129
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827613"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>IoT Edge modülünüzü hazırlayın teknik varlıkları hazırlama

Bu makalede, IoT Edge modülünün teknik varlıklarınızın Azure Marketi 'nde yayınlanmadan önce karşılaması gereken gereksinimler açıklanmaktadır.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>IoT Edge modüllerini anlama ve Başlarken

IoT Edge modülü, bir IoT Edge cihazında çalıştırılmak üzere yapılan Docker ile uyumlu bir kapsayıcıdır.

- IoT Edge modüller hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge modülleri anlama](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- IoT Edge modülünüzü geliştirmeye başlamak için bkz. [IoT Edge modülleri geliştirmeye yönelik gereksinimler ve araçlar](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Teknik gereksinimler

IoT Edge modülünüzün Azure Marketi 'nde sertifikalandırılması ve yayımlanması için aşağıdaki teknik gereksinimlerin karşılanması gerekir.

### <a name="platform-support"></a>Platform desteği

IoT Edge modülünüzün aşağıdaki platform seçeneklerinden birini desteklemesi gerekir.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge tarafından desteklenen katman 1 platformları

IoT Edge tarafından desteklenen tüm katman 1 platformlarını destekler ( [Azure IoT Edge desteğiyle](https://docs.microsoft.com/azure/iot-edge/support)kaydedilir). Daha iyi bir müşteri deneyimi sağladığından bu seçeneği öneririz. Bu ölçütlere uyan modüller görüntülenir. Bu platform seçeneğini kullanan bir modül şunları içermelidir:

- GitHub [bildirim aracı](https://github.com/estesp/manifest-tool)ile oluşturulmuş bildirim etiketleri olan bir `latest` etiketi ve sürüm etiketi (örneğin, `1.0.1`) sağlayın.
- [Uyumlu IoT Edge Sertifikalı cihazlara](https://aka.ms/iot-edge-certified)bağlantı eklemek için [Market sekmesini](./cpp-marketplace-tab.md) kullanın. Bu bağlantı, müşterilerin Sertifikalı cihazlara gözatabilecekleri veya arayabildiği bir Web sitesi olan `https://aka.ms/iot-edge-certified`olarak çözümlenir. Bu web sitesi, [Azure IoT Edge sertifikalı](https://catalog.azureiotsolutions.com/) cihaz kataloğu olarak da bilinir.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge tarafından desteklenen katman 1 platformların bir alt kümesi
  
IoT Edge tarafından desteklenen katman 1 platformların (en az bir) alt kümesini ( [Azure IoT Edge desteğiyle](https://docs.microsoft.com/azure/iot-edge/support)kaydedilir) destekler. Bu platform seçeneğini kullanan bir modül şunları içermelidir:

- Birden fazla platform destekleniyorsa, GitHub [bildirim aracı](https://github.com/estesp/manifest-tool) ile oluşturulmuş bir `latest` etiketi ve sürüm etiketi sağlayın (örneğin, `1.0.1`). Bildirim etiketleri yalnızca bir platform desteklensırada isteğe bağlıdır.
- [Azure IoT Edge sertifikalı](https://catalog.azureiotsolutions.com/) cihaz kataloğunda en az bir IoT Edge cihazına bağlantı sağlamak için [Market sekmesini](./cpp-marketplace-tab.md) kullanın.

### <a name="device-dimensions"></a>Cihaz boyutları

Hedeflenen IoT Edge cihazlarındaki IoT Edge modül boyutları (CPU/RAM/depolama/GPU/vb.) aşağıdaki gereksinimleri karşılamalıdır:

- Modülün [Azure IoT Edge sertifikalı](https://catalog.azureiotsolutions.com/) cihaz kataloğunda **en az bir IoT Edge sertifikalı cihazla çalışması** gerekir.
- **En düşük donanım gereksinimleri** , teklifin açıklamasında ( [Market sekmesi](./cpp-marketplace-tab.md)altında) son paragraf olarak belgelenmelidir. İsteğe bağlı olarak, önemli ölçüde farklılık gösterebilse de önerilen donanım gereksinimlerini listeleyebilirsiniz. Örneğin, teklif tanımlarınızın sonuna aşağıdaki bölümü ekleyin:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Yapılandırma

Ayrıca, IoT Edge bir cihaza dağıtımı mümkün olduğunca basit hale getirmek için varsayılan yapılandırma ayarlarını da içerir. Kapsayıcı Ayrıca, edgeHub ve IoT Hub iletişim sağlamak için IoT Edge modülü SDK 'sını de içerebilir.

#### <a name="default-configuration"></a>Varsayılan yapılandırma

IoT Edge modüller [, bulut Iş ortağı portalının SKU sekmesinde](./cpp-skus-tab.md)belirtilen varsayılan ayarlarla başlayabilmelidir. Aşağıdaki varsayılan ayarlar kullanılabilir:

- Varsayılan **yollar**
- Varsayılan **ikizi istenen özellikler**
- Varsayılan **ortam değişkenleri**
- Varsayılan **createOptions**

Varsayılan değer için gereken bir parametrenin anlamlı olmadığı bir senaryoda (örneğin, bir müşterinin sunucusunun IP adresi), varsayılan değer olarak bir parametre eklersiniz. Bu değer, köşeli ayraçlar ve büyük harfle alınır. Bu örnekte, aşağıdaki varsayılan ortam değişkenini ayarlarsınız:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Yapılandırma belgeleri

Bir IoT Edge modülünün tüm yapılandırma ayarları açıkça belgelenmelidir (yollarını kullanma, ikizi istenen özellikler, ortam değişkenleri, createOptions vb.) Belgelerinize bir bağlantı sağlayın veya belge teklif/SKU açıklamanız kapsamında olmalıdır.

### <a name="tags-and-versioning"></a>Etiketler ve sürüm oluşturma

Müşterilerin bir modülün kolayca dağıtılması ve marketten güncelleştirmeleri otomatik olarak alabilmesi gerekir (bir geliştirici senaryosunda.) Ayrıca, test ettikleri tam sürümü (bir üretim senaryosunda) kullanabilmek ve dondurmaları gerekir.

Bu müşterinin beklentilerini karşılamak ve Market 'te yayımlanmak için IoT Edge modüllerin aşağıdaki gereksinimleri karşılaması gerekir:

- Desteklenen tüm platformlarda en son sürümü işaret eden bir manifest `latest` etiketi ekleyin.
- Sürüm etiketleri X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır.
- Desteklenen tüm platformlarda belirli bir sürümü işaret eden `1.0.1`gibi bir "sürüm" etiketi ekleyin.
- `1.0.1`gibi "sürüm" etiketlerini güncelleştirmeyin çünkü sabit olmaları gerekir.

>[!Note]
>İsteğe bağlı olarak, sürüm oluşturma, `2.0` ve `1.0`gibi "sıralı sürüm" etiketleri içerebilir. Bu, paralel olarak birden çok ana sürümü korumanın kullanılmasını destekler.

### <a name="telemetry"></a>Telemetri

IoT modülü SDK 'sını kullanan modüller, telemetri amacıyla benzersiz modül tanımlayıcısını `PublisherId.OfferId.SkuId` olarak ayarlamış olmalıdır. Benzersiz bir tanımlayıcı, Azure Marketi 'nin çalışmakta olan modül örneklerinin sayısını belirlemesine olanak sağlar.

 `ProductInfo` bu tanımlayıcıya ayarlamak için IoT modülü SDK 'larında aşağıdaki yöntemleri kullanın:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

IoT modülü SDK 'sını kullanmayan modüller için, İndirme sayısı gibi Bulut İş Ortağı Portalı aracılığıyla daha az kesin Öngörüler sunulmaktadır.

### <a name="security"></a>Güvenlik

IoT Edge modüller, ana bilgisayara mümkün olduğunca en az ayrıcalıklı erişimi sormalıdır. [Ayrıcalıklı modüller](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) kaçınılmalıdır.

### <a name="module-iot-sdk"></a>Modül IoT SDK 'Sı

IoT modülü SDK 'Sı dahil, sertifika için bir önkoşul değildir. Ancak, IoT modülü SDK 'Sı dahil daha iyi bir kullanıcı deneyimi sağlayabilir. Örneğin, buluta yönlendirmeyi veya ileti göndermeyi desteklemek için.

Çalışan modül örneği sayısı hakkında telemetri verileri almak için IoT modülü SDK 'Sı gereklidir.


## <a name="recertification-process"></a>Yeniden sertifika işlemi

<!-- Add legal time windows-->
İş ortakları, modüllerini etkileyen bir değişiklik olduğunda bildirim alır, örneğin:

- IoT Edge tarafından desteklenen katman 1 işletim sistemi/mimari desteği matrisi
- IoT modülü SDK 'Sı
- IoT Edge çalışma zamanı
- IoT Edge modülü sertifika yönergeleri

İş ortaklarının modüllerini güncelleştirmek ve Bulut İş Ortağı Portalı aracını kullanarak onları yeniden bilmeleri gerekir.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>IoT Edge modülünüzü bir Azure Container Registry barındırın

IoT Edge modülünüzü Bulut İş Ortağı Portalı yüklemek için önce [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) içinde barındırın. Modül, bir bildirim etiketi tarafından başvurulan görüntü etiketleri dahil olmak üzere yayımlamak istediğiniz tüm etiketleri içermelidir.


## <a name="next-steps"></a>Sonraki adımlar

- [IoT Edge modülü Teklifinizi oluşturma](./cpp-create-offer.md)

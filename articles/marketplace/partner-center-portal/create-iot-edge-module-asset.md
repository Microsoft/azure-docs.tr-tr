---
title: IoT Edge modülünüzü hazırlama teknik varlıkları-Azure Marketi
description: Azure Market 'Te yayımlayabilmeniz için Nesnelerin İnterneti (IoT) Edge modülünün teknik varlıklarının karşılaması gereken teknik ve yapılandırma gereksinimleri hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: f8eddd5d346003af0c2b41d65fc0aa09c86f48c7
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167456"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>IoT Edge modülünüzü hazırlayın teknik varlıkları hazırlama

Bu makalede, Azure Marketi 'nde yayınlanmadan önce Nesnelerin İnterneti (IoT) Edge modülünün teknik varlıklarının karşılaması gereken gereksinimler açıklanmaktadır.

## <a name="get-started"></a>başlarken

IoT Edge modülü, bir IoT Edge cihazında çalışan Docker ile uyumlu bir kapsayıcıdır.

- IoT Edge modüller hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge modülleri anlama](../../iot-edge/iot-edge-modules.md).
- IoT Edge modülünüzü geliştirmeye başlamak için bkz. [kendi IoT Edge modüllerinizi geliştirme](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Teknik gereksinimler

IoT Edge modülünüzün Azure Marketi 'nde sertifikalandırılması ve yayımlanması için aşağıdaki teknik gereksinimleri karşılaması gerekir.

### <a name="platform-support"></a>Platform desteği

IoT Edge modülünüzün aşağıdaki platform seçeneklerinden birini desteklemesi gerekir:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge tarafından desteklenen katman 1 platformları

Modülünüzün IoT Edge tarafından desteklenen tüm katman 1 platformlarını desteklemesi gerekir ( [Azure IoT Edge destek](../../iot-edge/support.md)'te kaydedilir). Daha iyi bir müşteri deneyimi sağladığından bu seçeneği öneririz. Bu ölçütleri karşılayan modüller görüntülenir. Bu platform seçeneğini kullanan bir modül şunları içermelidir:

- [GitHub bildirim aracı](https://github.com/estesp/manifest-tool)ile oluşturulmuş bildirim etiketleri olan en son bir etiketi ve sürüm etiketini (örneğin, 1.0.1) belirtin.

- [Azure IoT Edge sertifikalı cihaz kataloğuna](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) **yararlı bağlantılar** bölümünün altına bir bağlantı eklemek için [iş ortağı merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace) 'nde teklif listesi sekmesini kullanın.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge tarafından desteklenen katman 1 platformların bir alt kümesi

Modülünüzün IoT Edge tarafından desteklenen katman 1 platformların bir alt kümesini (en az bir adet) desteklemesi gerekir ( [Azure IoT Edge destek](../../iot-edge/support.md)'te kaydedilir). Bu platform seçeneğini kullanan bir modül şunları içermelidir:

- Birden fazla platform destekleniyorsa, GitHub [bildirim aracı](https://github.com/estesp/manifest-tool) ile oluşturulmuş bildirim etiketleri olan en son bir etiketi ve sürüm etiketini (örneğin, 1.0.1) belirtin. Bildirim etiketleri yalnızca bir platform desteklensırada isteğe bağlıdır.
- **Faydalı bağlantılar** bölümünün altında, [Azure IoT Edge sertifikalı cihaz kataloğundan](https://devicecatalog.azure.com/)en az bir IoT Edge cihaza bir bağlantı eklemek için [iş ortağı merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace) ' ndeki teklif listesi sekmesini kullanın.

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Bu, Iş Ortağı Merkezi 'nde teklif listesi bölümünün görüntüsüdür":::

### <a name="device-dimensions"></a>Cihaz boyutları

Hedeflenen IoT Edge cihazlarda IoT Edge modül boyutlarının (CPU, RAM, depolama ve GPU gibi) aşağıdaki gereksinimleri karşılaması gerekir:

- Modül, [Azure IoT Edge sertifikalı cihaz kataloğundan](https://devicecatalog.azure.com/)en az bir IoT Edge cihazından çalışmalıdır.

- En düşük donanım gereksinimleri, teklifin açıklamasında ( [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace)'nde teklif listesi sekmesi altında) son paragraf olarak belgelenmelidir. İsteğe bağlı olarak, önemli ölçüde farklılık gösterebilse de önerilen donanım gereksinimlerini listeleyebilirsiniz. Örneğin, teklif tanımlarınızın sonuna aşağıdaki bölümü ekleyin:

Bu HTML metnini kopyalayın veya Düzen penceresindeki ilgili zengin metin işlevlerini kullanın.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Yapılandırma

Modülünüzün IoT Edge dağıtımı olabildiğince basit hale getirmek için varsayılan yapılandırma ayarlarını içermesi gerekir. Bu bilgiler, [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace)'nde plan için **Teknik yapılandırma** sayfasında bulunabilir. Kapsayıcı, uç hub ve IoT Hub iletişim sağlamak için IoT Edge modülü SDK 'sını de içerebilir.

#### <a name="default-configuration"></a>Varsayılan yapılandırma

IoT Edge modüller, [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace)'nde plan için **Teknik yapılandırma** sayfasında belirtilen varsayılan ayarlarla başlayabilmelidir. Aşağıdaki varsayılan ayarlar kullanılabilir:

- Varsayılan **yollar**
- Varsayılan **Modül ikizi istenen özellikler**
- Varsayılan **ortam değişkenleri**
- Varsayılan **kapsayıcı oluşturma seçenekleri**

Varsayılan bir değer için gereken bir parametresinin anlamlı olmadığı bir senaryoda (örneğin, bir müşterinin sunucusunun IP adresi), varsayılan değer olarak bir parametre ekleyin. Bu değer, büyük harf ve köşeli ayraçlar içine alınmıştır. Bu örnekte, aşağıdaki varsayılan ortam değişkenini ayarlarsınız:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Yapılandırma belgeleri

Bir IoT Edge modülünün tüm yapılandırma ayarları açıkça belgelenmelidir. Örneğin, onun yollarını nasıl kullanacağınızı, ikizi istenen özellikleri, ortam değişkenlerini, createOptions vb. belgeyi belgemalısınız. Belgelerinize bir bağlantı sağlamanız veya teklif ya da plan açıklamanızı bir parçası yapmanız gerekir. Bu bilgileri [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace)'nde **teklif listesi** ve **planı listeleme** sayfasında sağlayabilirsiniz.

#### <a name="tags-and-versioning"></a>Etiketler ve sürüm oluşturma

Müşterilerin bir modülün kolayca dağıtılması ve marketten (bir geliştirici senaryosunda) güncelleştirmeleri otomatik olarak alabilmesi gerekir. Ayrıca, test ettikleri tam bir sürümü (bir üretim senaryosunda) kullanabilmeleri ve dondurmaları gerekir.

Bu müşteri beklentilerini karşılamak ve Market 'te yayımlanmak için IoT Edge modüllerin aşağıdaki gereksinimleri karşılaması gerekir

- Desteklenen tüm platformlarda en son sürümü gösteren bir bildirim en son etiketini ekleyin.
- X. Y. Z biçiminde sürüm etiketleri oluşturun, burada X, Y ve Z tamsayılardır.
- Desteklenen tüm platformlarda belirli bir sürümü işaret eden 1.0.1 gibi bir "sürüm" etiketi ekleyin.
- Değiştirilmeleri gerektiğinden, 1.0.1 gibi "sürüm" etiketlerini güncelleştirmeyin.

> [!NOTE]
> İsteğe bağlı olarak, sürüm oluşturma, 2,0 ve 1,0 gibi "sıralı sürüm" etiketlerini içerebilir. Bu, paralel olarak birden çok ana sürümü korumanın kullanılmasını destekler.

### <a name="telemetry"></a>Telemetri

IoT modülü SDK 'sını kullanan modüller, telemetri amaçları için benzersiz modül tanımlayıcısını publisherID. OfferId. SkuId olarak ayarlanmalıdır. Benzersiz bir tanımlayıcı, Azure Marketi 'nin çalışmakta olan modül örneklerinin sayısını belirlemesine yardımcı olur.

ProductInfo 'yu bu tanımlayıcıya ayarlamak için IoT modülü SDK 'larında aşağıdaki yöntemlerden birini kullanın:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

IoT modülü SDK 'sını kullanmayan modüller için, İndirme sayısı gibi Iş Ortağı Merkezi aracılığıyla daha az kesin Öngörüler bulabilirsiniz.

### <a name="security"></a>Güvenlik

IoT Edge modüller [ayrıcalıklı modüllerden](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)kaçınmalıdır. Bunun yerine, ana bilgisayara mümkün olduğunca en az ayrıcalıklı erişim isteyin.

### <a name="module-iot-sdk"></a>Modül IoT SDK 'Sı

IoT modülü SDK 'Sı dahil, sertifika için bir önkoşul değildir. Ancak, IoT modülü SDK 'Sı dahil daha iyi bir kullanıcı deneyimi sağlayabilir. Örneğin, buluta yönlendirmeyi veya ileti göndermeyi desteklemek için.

IoT modülü SDK 'Sı, çalışmakta olan modül örneklerinin sayısı hakkında telemetri verileri almak için gereklidir.

## <a name="recertification-process"></a>Yeniden sertifika işlemi

İş ortakları, modüllerini etkileyen bir değişiklik olduğunda bilgilendirilir, örneğin:

- IoT Edge tarafından desteklenen katman 1 işletim sistemi/mimari desteği matrisi
- IoT modülü SDK 'Sı
- IoT Edge çalışma zamanı
- IoT Edge modülü sertifika yönergeleri

İş ortaklarının, [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace)'nde yeniden yayımlayarak tekliflerini güncelleştirmesi ve etkinleştirmeleri gerekir.

Ayrıca, yeni resim etiketleri ekleme gibi bir güncelleştirme yaparsanız teklifiniz de silinir.

## <a name="host-module-in-azure-container-registry"></a>Azure Container Registry 'de ana bilgisayar modülü

IoT Edge modülünüzü Azure Market 'e yüklemek için, önce bunu bir [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) barındıralmanız gerekir. Modül, bir bildirim etiketi tarafından başvurulan görüntü etiketleri dahil olmak üzere yayımlamak istediğiniz tüm etiketleri içermelidir. Daha fazla bilgi için bkz. [Azure Container Registry oluşturma ve kapsayıcı görüntüsü gönderme](../../container-instances/container-instances-tutorial-prepare-acr.md)öğreticisi.

## <a name="next-steps"></a>Sonraki adımlar

- [IoT Edge modül teklifi oluşturma](azure-iot-edge-module-creation.md)
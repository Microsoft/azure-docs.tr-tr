---
title: Azure için Microsoft bağlı önbelleği güncelleştirme IoT Hub yapılandırma | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Azure IoT Hub cihaz güncelleştirmesi için Microsoft bağlı önbelleği 'ne genel bakış
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811847"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Azure için cihaz güncelleştirmesi için Microsoft bağlı önbelleğini yapılandırma IoT Hub

Microsoft bağlı önbelleği, Azure IoT Edge bir modül olarak Azure IoT Edge ağ geçitlerine dağıtılır. Diğer Azure IoT Edge modüller gibi, Microsoft bağlı önbellek modüllerini yapılandırmak için MCC modül dağıtım ortamı değişkenleri ve kapsayıcı oluşturma seçenekleri kullanılır.  Bu bölüm, bir müşterinin Microsoft bağlı önbellek modülünü Azure IoT Hub cihaz güncelleştirmesi tarafından kullanılmak üzere başarıyla dağıtması için gereken ortam değişkenlerini ve kapsayıcı oluşturma seçeneklerini tanımlar.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft bağlı önbellek Azure IoT Edge modülü dağıtım ayrıntıları

Microsoft bağlı önbellek modülünün adlandırılması, yöneticinin kararına sahiptir. İletişim için modülün adını kullanan başka bir modül veya hizmet etkileşimi yoktur. Ek olarak, Microsoft bağlı önbellek sunucularının üst alt ilişkisi bu modül adına bağımlı değildir, bunun yerine daha önce anlatıldığı gibi yapılandırılmış Azure IoT Edge ağ geçidinin FQDN veya IP adresidir.

Microsoft bağlı önbellek Azure IoT Edge modülü ortam değişkenleri, temel modül kimlik bilgilerini ve işlevsel modül ayarlarını kapsayıcıya geçirmek için kullanılır.

| Değişken Adı                 | Değer biçimi                           | Gerekli/İsteğe Bağlı | İşlev                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure abonelik KIMLIĞI GUID 'SI             | Gerekli          | Bu, güvenli bir şekilde sağlanan, müşterinin anahtarıdır<br>Dağıtım Iyileştirmesi için önbellek düğümünün kimlik doğrulaması<br>Servislere.<br>Modülün çalışması için gereklidir. |
| CACHE_NODE_ID                 | Önbellek düğüm KIMLIĞI GUID 'SI                     | Gerekli          | Microsoft bağlı önbelleğini benzersiz şekilde tanımlar<br>Teslim Iyileştirme Hizmetleri için düğüm.<br>İçin gerekli<br> modülün çalışması için. |
| CUSTOMER_KEY                  | Müşteri anahtarı GUID 'SI                     | Gerekli          | Bu, güvenli bir şekilde sağlanan, müşterinin anahtarıdır<br>En Iyi duruma getirme hizmetlerini teslim etmek için önbellek düğümünün kimlik doğrulaması.<br>Modülün çalışması için gereklidir.|
| STORAGE_ *N* _SIZE_GB           | Burada N, önbellek sürücüsüdür   | Gerekli          | İçeriği önbelleğe almak için 9 adede kadar sürücü belirtin ve içindeki en fazla alanı belirtin<br>Her önbellek sürücüsünde içerik ayrılacak gigabayt. Örnekler:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Sürücü sayısı, belirtilen önbellek sürücüsü bağlama değerleriyle aynı olmalıdır<br>Kapsayıcı oluştur seçeneği için MicrosoftConnectedCache *N* değeri<br>Önbelleğin en küçük boyutu 10 GB 'dir.|
| UPSTREAM_HOST                 | FQDN/ıP                                | İsteğe Bağlı          | Bu değer, Microsoft 'a bağlı bir yukarı akış belirtebilir<br>Bağlı önbellek düğümü varsa proxy görevi gören önbellek düğümü<br> İnternet bağlantısı kesildi. Bu ayar, desteklemek için kullanılır<br> Iç Içe IoT senaryosu.<br>**Note:** Microsoft bağlı önbelleği, http varsayılan bağlantı noktası 80 ' i dinler.|
| UPSTREAM_PROXY                | FQDN/ıP: BAĞLANTı NOKTASı                           | İsteğe Bağlı          | Giden internet proxy 'si.<br>Bu, bir ISA 95 ağı ise OT DMZ proxy de olabilir. |
| CACHEABLE_CUSTOM_ *N* _HOST     | ANA BILGISAYAR/ıP<br>FQDN                        | İsteğe Bağlı          | Özel paket depolarını desteklemek için gereklidir.<br>Depolar yerel olarak veya internet üzerinde barındırılabilir.<br>Yapılandırılabilecek özel ana bilgisayar sayısı için bir sınır yoktur.<br><br>Örnekler:<br>Ad = CACHEABLE_CUSTOM_1_HOST değer = packages.foo.com<br> Ad = CACHEABLE_CUSTOM_2_HOST değer = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Diğer ad                                  | İsteğe Bağlı          | Özel paket depolarını desteklemek için gereklidir.<br>Bu değer, bir diğer ad olarak kullanılabilir ve önbellek sunucusu tarafından başvurmak üzere kullanılacaktır<br>farklı DNS adları. Örneğin, depo içeriği ana bilgisayar adı packages.foo.com olabilir,<br>Ancak, farklı bölgelerde ana bilgisayar adına eklenen ek bir önek olabilir<br>westuscdn.packages.foo.com ve eastuscdn.packages.foo.com gibi.<br>Kurallı diğer adı ayarlayarak içeriğin yinelenmediğinden emin olursunuz<br>aynı konaktan gelen içerikler, ancak farklı CDN kaynakları için.<br>Kurallı değerin biçimi önemli değildir, ancak konağın benzersiz olması gerekir.<br>Değeri ana bilgisayar değeriyle eşleşecek şekilde ayarlamak en kolay olabilir.<br><br>Yukarıdaki özel ana bilgisayar örneklerine göre örnekler:<br>Ad = CACHEABLE_CUSTOM_1_CANONICAL değer = foopackages<br> Ad = CACHEABLE_CUSTOM_2_CANONICAL değer = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | True veya false                          | İsteğe Bağlı          | Yerel ağ veya internet 'te Özet raporun görüntülenmesini mümkün.<br>Bir API anahtarı (daha sonra ele alınmıştır) kullanımı, doğru olarak ayarlandıysa özet raporu görüntülemek için gereklidir. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| True veya false                          | İsteğe Bağlı          | Özet raporun yerel ağda veya internet 'te olmadan görüntülenmesine izin vermez<br>ağ üzerindeki herhangi bir cihazdan API anahtarı kullanımı. Erişimi kilitlemek istemiyorsanız kullanın<br>önbellek sunucusu özet verilerini tarayıcı aracılığıyla görüntülemek için. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft bağlı önbellek Azure IoT Edge modülü kapsayıcı oluşturma seçenekleri

MCC modül dağıtımı için kapsayıcı oluşturma seçenekleri, MCC modülü tarafından kullanılan depolama ve bağlantı noktalarıyla ilgili ayarların denetimini sağlar. Bu, MCC dağıtımı için kullanılan gerekli kapsayıcı tarafından oluşturulan değişkenlerin listesidir.

### <a name="container-to-host-os-drive-mappings"></a>Konak işletim sistemi sürücü eşlemeleri için kapsayıcı

Kapsayıcı depolama konumunu diskteki depolama konumuyla eşlemek için gereklidir. en fazla dokuz konum belirtilebilir <.

>[!Note]
>Sürücü numarası, ortam değişkeninde belirtilen önbellek sürücüsü bağlama değerleriyle eşleşmelidir STORAGE_ *N* _SIZE_GB değer, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>TCP bağlantı noktası eşlemelerini barındıracak kapsayıcı

Bu seçenek, içerik istekleri için MCC tarafından dinlediği dış makine http bağlantı noktasını belirtir. Varsayılan HostPort, bağlantı noktası 80 ve diğer bağlantı noktaları, ADU istemcisi bağlantı noktası 80 ' de istek yaptığı için şu anda desteklenmiyor. TCP bağlantı noktası 8081, MCC 'nin dinlediği ve değiştirilemeyen iç kapsayıcı bağlantı noktasıdır.

### <a name="container-service-tcp-port-mappings"></a>Kapsayıcı hizmeti TCP bağlantı noktası eşlemeleri

Microsoft bağlı önbellek modülünün, çeşitli işlevler için önbelleğe alma altyapısı tarafından kullanılan bir .NET Core hizmeti vardır.

>[!Note]
>Azure IoT Iç Içe kenarını desteklemek için, HostPort 5000 olarak ayarlanmamalıdır, çünkü kayıt defteri proxy modülü zaten 5000 ana bilgisayar bağlantı noktasını dinliyor.


Örnek kapsayıcı oluşturma seçenekleri

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft bağlı önbellek özet raporu

Özet raporu şu anda bir müşterinin Azure IoT Edge ağ geçitlerine dağıtılan Microsoft bağlı önbellek örnekleri için önbelleğe alma verilerini görüntüleme yoludur. Rapor, 15 saniyelik aralıklarla oluşturulur ve dönemin ortalama istatistiklerini ve ayrıca modülün kullanım ömrü için toplanan istatistikleri içerir. Müşterilerin ilgilendiği ana istatistikler şunlardır:

* **hitbytes** -bu, doğrudan önbellekten gelen gönderilen baytların toplamıdır.
* **Hatalı sbytes** -bu, önbelleği görmek Için Microsoft bağlı önbelleğinin CDN 'den indirmesi gereken toplam bayt toplamıdır.
* **Eggressbytes** -bu, Hitbytes ve hatalı baytların toplamıdır ve istemcilere teslim edilen toplam bayttır.
* **hitRatioBytes** -bu, Hitbytes 'In Yumurressbytes olarak oranıdır.  Bir dönemde teslim edilen eggressBytes %100, Itbytes 'a eşitse bu, örneğin 1 olacaktır.


Özet raporu, `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` \<Azure IoT Edge Gateway IP\> IoT Edge Ağ GEÇIDINIZIN IP adresi veya ana bilgisayar adı ile değiştirilir. (Bu raporun görünürlüğü hakkında bilgi için ortam değişkeni ayrıntılarına bakın).
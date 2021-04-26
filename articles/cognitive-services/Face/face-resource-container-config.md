---
title: Kapsayıcıları yapılandırma-yüz
titleSuffix: Azure Cognitive Services
description: Yüz kapsayıcı çalışma zamanı ortamı, `docker run` komut bağımsız değişkenleri kullanılarak yapılandırılır. Hem gerekli hem de isteğe bağlı ayarlar vardır.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5287fe6df4a2f0cb728bf0e705a4ce989ca38487
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934045"
---
# <a name="configure-face-docker-containers"></a>Yüz Docker kapsayıcılarını yapılandırma

**Yüz** kapsayıcı çalışma zamanı ortamı, `docker run` komut bağımsız değişkenleri kullanılarak yapılandırılır. Bu kapsayıcıda bazı gerekli ayarlar ve bazı isteğe bağlı ayarlar vardır. Birkaç komuta [örnek](#example-docker-run-commands) vardır. Kapsayıcıya özgü ayarlar faturalandırma ayarlardır. 

## <a name="configuration-settings"></a>Yapılandırma ayarları

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) Ve [`Eula`](#eula-setting) ayarları birlikte kullanılır ve üçü için geçerli değerler sağlamanız gerekir; Aksi takdirde Kapsayıcınız başlatılmaz. Bir kapsayıcı oluşturmak için bu yapılandırma ayarlarını kullanma hakkında daha fazla bilgi için bkz. [faturalandırma](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey yapılandırma ayarı

Bu `ApiKey` ayar, kapsayıcının fatura bilgilerini izlemek için kullanılan Azure Kaynak anahtarını belirtir. ApiKey için bir değer belirtmeniz gerekir ve değerin yapılandırma ayarı için belirtilen bilişsel _Hizmetler_ kaynağı için geçerli bir anahtar olması gerekir [`Billing`](#billing-configuration-setting) .

Bu ayar aşağıdaki yerde bulunabilir:

* Azure portal: bilişsel **Hizmetler** kaynak yönetimi, **anahtarlar** altında

## <a name="applicationinsights-setting"></a>ApplicationInsights ayarı

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Faturalandırma yapılandırma ayarı

`Billing`Ayar, Azure üzerinde bulunan bilişsel _Hizmetler_ kaynağının, kapsayıcının fatura bilgilerini ölçmek için kullanılan uç nokta URI 'sini belirtir. Bu yapılandırma ayarı için bir değer belirtmeniz gerekir ve Azure 'daki bilişsel _Hizmetler_ kaynağı için değer geçerli bir uç nokta URI 'si olmalıdır. Kapsayıcı her 10 ila 15 dakikada bir kullanım raporu sağlar.

Bu ayar aşağıdaki yerde bulunabilir:

* Azure portal: bilişsel **Hizmetler** genel bakış, etiketli `Endpoint`

_Yüz_ yönlendirmeyi, örnekte gösterildiği gibi uç nokta URI 'sine eklemeyi unutmayın. 

|Gerekli| Name | Veri türü | Açıklama |
|--|------|-----------|-------------|
|Evet| `Billing` | Dize | Faturalama uç noktası URI 'SI. Faturalandırma URI 'sini alma hakkında daha fazla bilgi için bkz. [gerekli parametreleri toplama](face-how-to-install-containers.md#gathering-required-parameters). Daha fazla bilgi ve bölgesel uç noktaların tamamen listesi için bkz. bilişsel [Hizmetler Için özel alt etki alanı adları](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI yapılandırma ayarları

Bölümündeki yapılandırma ayarları, `CloudAI` kapsayıcınıza özel kapsayıcıya özgü seçenekler sağlar. Aşağıdaki ayarlar ve nesneler, bölümündeki yüz kapsayıcısı için desteklenir `CloudAI`

| Name | Veri türü | Açıklama |
|------|-----------|-------------|
| `Storage` | Nesne | Yüz kapsayıcısı tarafından kullanılan depolama senaryosu. Depolama senaryoları ve nesnenin ilişkili ayarları hakkında daha fazla bilgi için `Storage` bkz. [depolama senaryosu ayarları](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Depolama senaryosu ayarları

Yüz kapsayıcısı, depolanmaya bağlı olarak blob, önbellek, meta veri ve kuyruk verilerini depolar. Örneğin, bir **Largepersongroup** için eğitim dizinleri ve sonuçları blob verileri olarak depolanır. Yüz kapsayıcısı, bu veri türlerini kullanarak ve depolarken iki farklı depolama senaryosu sağlar:

* Bellek  
  Dört tür veri, bellekte depolanır. Bunlar dağıtılmaz ve kalıcı değildir. Yüz kapsayıcısı durdurulmuşsa veya kaldırılırsa, o kapsayıcı için depolama alanındaki tüm veriler yok edilir.  
  Bu, yüz kapsayıcısı için varsayılan depolama senaryosudur.
* Azure  
  Yüz kapsayıcısı, bu dört veri türünü kalıcı depolama boyunca dağıtmak için Azure Storage ve Azure Cosmos DB kullanır. Blob ve kuyruk verileri Azure depolama tarafından işlenir. Meta veriler ve önbellek verileri Azure Cosmos DB tarafından işlenir. Yüz kapsayıcısı durdurulmuşsa veya kaldırılırsa, bu kapsayıcı için depolama alanındaki tüm veriler Azure depolama alanında depolanır ve Azure Cosmos DB.  
  Azure depolama senaryosunda kullanılan kaynaklar aşağıdaki ek gereksinimlere sahiptir
  * Azure depolama kaynağı StorageV2 hesap türünü kullanmalıdır
  * Azure Cosmos DB kaynağı MongoDB için Azure Cosmos DB API 'sini kullanmalıdır

Depolama senaryoları ve ilişkili yapılandırma ayarları, `Storage` nesne tarafından, `CloudAI` yapılandırma bölümü altında yönetilir. Aşağıdaki yapılandırma ayarları `Storage` nesnede mevcuttur:

| Name | Veri türü | Açıklama |
|------|-----------|-------------|
| `StorageScenario` | Dize | Kapsayıcı tarafından desteklenen depolama senaryosu. Aşağıdaki değerler mevcuttur<br/>`Memory` -Varsayılan değer. Kapsayıcı, tek düğümlü, geçici kullanım için kalıcı olmayan, dağıtılmamış ve bellek içi depolama kullanır. Kapsayıcı durdurulmuşsa veya kaldırılırsa, o kapsayıcının depolama alanı yok edilir.<br/>`Azure` -Container depolama için Azure kaynaklarını kullanır. Kapsayıcı durdurulmuşsa veya kaldırılırsa, o kapsayıcının depolaması kalıcı olur.|
| `ConnectionStringOfAzureStorage` | Dize | Kapsayıcı tarafından kullanılan Azure depolama kaynağı için bağlantı dizesi.<br/>Bu ayar yalnızca `Azure` yapılandırma ayarı için belirtilmişse geçerlidir `StorageScenario` . |
| `ConnectionStringOfCosmosMongo` | Dize | Kapsayıcı tarafından kullanılan Azure Cosmos DB kaynak için MongoDB bağlantı dizesi.<br/>Bu ayar yalnızca `Azure` yapılandırma ayarı için belirtilmişse geçerlidir `StorageScenario` . |

Örneğin, aşağıdaki komut Azure Storage senaryosunu belirtir ve Azure depolama için örnek bağlantı dizeleri ve yüz kapsayıcısının verilerini depolamak için kullanılan Cosmos DB kaynakları sağlar.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Depolama senaryosu, giriş takmaları ve çıkış taklarından ayrı olarak işlenir. Tek bir kapsayıcı için bu özelliklerin bir birleşimini belirtebilirsiniz. Örneğin, aşağıdaki komut, `D:\Output` Çıkış bağlama olarak ana makinedeki klasöre Docker BIND bağlamasını tanımlar, ardından, günlük dosyalarını JSON biçiminde çıkış bağlamasından kaydederek yüz kapsayıcı görüntüsünden bir kapsayıcı oluşturur. Bu komut ayrıca Azure Storage senaryosunu belirtir ve Azure depolama için örnek bağlantı dizeleri ve yüz kapsayıcısı için veri depolamak için kullanılan Cosmos DB kaynakları sağlar.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>EULA ayarı

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Akışkan entd ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http proxy kimlik bilgileri ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Oturum açma ayarları
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Bağlama ayarları

Kapsayıcıya ve kapsayıcılardan veri okumak ve buradan veri yazmak için BIND bağlama kullanın. `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunda seçeneğini belirterek bir giriş bağlama veya çıkış bağlama belirtebilirsiniz.

Yüz kapsayıcıları, eğitim veya hizmet verilerini depolamak için giriş veya çıkış taklarını kullanmaz. 

Konak bağlama konumunun tam sözdizimi, ana bilgisayar işletim sistemine bağlı olarak değişir. Ayrıca, Docker hizmeti hesabı ve konak bağlama konumu izinleri tarafından kullanılan izinler arasındaki bir çakışma nedeniyle [ana bilgisayarın](face-how-to-install-containers.md#the-host-computer)bağlama konumu erişilebilir olmayabilir. 

|İsteğe Bağlı| Name | Veri türü | Açıklama |
|-------|------|-----------|-------------|
|İzin verilmiyor| `Input` | Dize | Yüz kapsayıcıları bunu kullanmaz.|
|İsteğe Bağlı| `Output` | Dize | Çıkış bağlama hedefi. `/output` varsayılan değerdir. Bu, günlüklerin konumudur. Bu, kapsayıcı günlüklerini içerir. <br><br>Örnek:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Örnek Docker Run komutları 

Aşağıdaki örneklerde, komutlarının nasıl yazılacağını ve kullanılacağını göstermek için yapılandırma ayarları kullanılır `docker run` .  Çalışan bir kez, kapsayıcıyı [durduruncaya](face-how-to-install-containers.md#stop-the-container) kadar çalışmaya devam eder.

* **Satır devamlılık karakteri**: aşağıdaki bölümlerdeki Docker komutları, `\` satır devamlılık karakteri olarak ters eğik çizgi kullanır. Bunu, ana bilgisayar işletim sisteminizin gereksinimlerine göre değiştirin veya kaldırın. 
* **Bağımsız değişken sırası**: Docker Kapsayıcıları hakkında bilginiz yoksa bağımsız değişkenlerin sırasını değiştirmeyin.

{_Argument_name_} değerini kendi değerlerinizle değiştirin:

| Yer tutucu | Değer | Biçim veya örnek |
|-------------|-------|---|
| **{API_KEY}** | `Face`Azure anahtarları sayfasında kaynağın uç nokta anahtarı `Face` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Faturalandırma uç noktası değeri, Azure `Face` Genel Bakış sayfasında bulunur.| Açık örnekler için [gerekli parametreleri toplama](face-how-to-install-containers.md#gathering-required-parameters) konusuna bakın. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](face-how-to-install-containers.md#billing).
> ApiKey değeri, Azure  `Cognitive Services` kaynak anahtarları sayfasından alınan anahtardır. 

## <a name="face-container-docker-examples"></a>Yüz kapsayıcı Docker örnekleri

Aşağıdaki Docker örnekleri, yüz kapsayıcısı için verilmiştir. 

### <a name="basic-example"></a>Temel örnek 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Günlüğe kaydetme örneği 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcıları yüklemeyi ve çalıştırmayı](face-how-to-install-containers.md) inceleyin

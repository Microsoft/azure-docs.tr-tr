---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554701"
---
Aşağıdaki Azure kaynaklarından birini oluşturarak anomali algılayıcı hizmetini kullanmaya başlayın.

* [Deneme kaynağı](https://azure.microsoft.com/try/cognitive-services/#decision) (Azure aboneliği gerekmez): 
    * Ücretsiz olarak yedi gün için geçerlidir. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)bir deneme anahtarı ve uç noktası kullanılabilir olacaktır. 
    * Anomali algılayıcısı denemek istiyorsanız, Azure aboneliğiniz yoksa bu harika bir seçenektir.

* [Anomali algılayıcı kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * Kaynak silininceye kadar [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) ile kullanılabilir.
    * Ücretsiz fiyatlandırma katmanını kullanarak hizmeti deneyin ve daha sonra üretime yönelik ücretli bir katmana yükseltin.

### <a name="create-an-environment-variable"></a>Ortam değişkeni oluşturma

>[!NOTE]
> 1 Temmuz 2019 ' den sonra oluşturulan deneme olmayan kaynaklar için uç noktalar aşağıda gösterilen özel alt etki alanı biçimini kullanır. Daha fazla bilgi ve bölgesel uç noktaların tamamen listesi için bkz. bilişsel [Hizmetler Için özel alt etki alanı adları](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Oluşturduğunuz kaynaktan anahtarınızı ve uç noktayı kullanarak, kimlik doğrulama için iki ortam değişkeni oluşturun:

* `ANOMALY_DETECTOR_KEY`-isteklerinizin kimliğini doğrulamak için kaynak anahtarı.
* `ANOMALY_DETECTOR_ENDPOINT`-API isteklerinin gönderilmesi için kaynak uç noktası. Şöyle görünür: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

İşletim sisteminiz için yönergeleri kullanın.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Ortam değişkenini ekledikten sonra konsol penceresini yeniden başlatın.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source ~/.bashrc` çalıştırın.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

@No__t_0 düzenleyin ve ortam değişkenini ekleyin:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source .bash_profile` çalıştırın.

***
---
title: Azure 'da azaltma hatalarını giderme | Microsoft Docs
description: Azure Işlem 'da hataları, yeniden denemeyi ve geri dönüşü azaltma.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: db1c6e8e4f1e98db08d5f7ff0ef218fa42d25860
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103293"
---
# <a name="troubleshooting-api-throttling-errors"></a>API azaltma hatalarında sorun giderme 

Azure Işlem istekleri, hizmetin genel performansı konusunda yardımcı olmak için bir abonelikte ve bölgeye göre kısıtlanabilir. Microsoft. COMPUTE ad alanı, izin verilen en yüksek API istek hızını aşmadığı için Azure Işlem kaynak sağlayıcısı 'na (CRP) yönelik tüm çağrıların yapıldığından emin veriyoruz. Bu belgede API azaltma, azaltma sorunlarını gidermeye ilişkin ayrıntılar ve kısıtlanmamak için en iyi uygulamalar açıklanmaktadır.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Azure Resource Manager vs kaynak sağlayıcılarına göre daraltma  

Azure 'un ön kapısı olarak Azure Resource Manager, tüm gelen API isteklerinin kimlik doğrulaması ve ilk sıra doğrulaması ve azaltmasından oluşur. Azure Resource Manager çağrı hızı sınırları ve ilgili tanılama yanıtı HTTP üstbilgileri [burada](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits)açıklanmıştır.
 
Bir Azure API istemcisi bir azaltma hatası aldığında, HTTP durumu çok fazla Istek 429 ' dir. İstek azaltma 'nın Azure Resource Manager veya CRP gibi temel bir kaynak sağlayıcısı tarafından gerçekleştirilip yapılmlamadığını anlamak için get istekleri `x-ms-ratelimit-remaining-subscription-reads` ve `x-ms-ratelimit-remaining-subscription-writes` yanıt üst bilgilerini get istekleri için inceleyin. Kalan çağrı sayısı 0 ' a yaklaşıyorsa, aboneliğin Azure Resource Manager tarafından tanımlanan genel çağrı sınırına ulaşıldı. Tüm abonelik istemcilerine göre etkinlikler birlikte sayılır. Aksi takdirde, daraltma hedef kaynak sağlayıcısından (istek URL 'sinin `/providers/<RP>` segmenti tarafından adreslenmiş olan) geliyor. 

## <a name="call-rate-informational-response-headers"></a>Çağrı hızı bilgilendirici yanıt üstbilgileri 

| Üstbilgi                            | Değer biçimi                           | Örnek                               | Açıklama                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-MS-ratelimit-kalan-kaynak |```<source RP>/<policy or bucket>;<count>```| Microsoft. COMPUTE/HighCostGet3Min; 159 | Bu isteğin hedefi dahil olmak üzere kaynak demeti veya işlem grubunu kapsayan Azaltma ilkesi için kalan API çağrısı sayısı                                                                   |
| x-MS-istek ücreti               | ```<count>```                             | 1\.                                     | Bu HTTP isteğinin geçerli ilke sınırına doğru "ücretlendirilen" çağrı sayısı. Bu en genellikle 1 ' dir. Bir sanal makine ölçek kümesinin ölçeklendirilmesi gibi Batch istekleri birden fazla sayı ücretlendirilmesine sahip olabilir. |


Bir API isteğinin birden fazla daraltma ilkesine tabi olabileceğini unutmayın. Her ilke için ayrı `x-ms-ratelimit-remaining-resource` bir üst bilgi olacaktır. 

Sanal makine ölçek kümesi isteğini silmenin örnek bir yanıtı aşağıda verilmiştir.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Azaltma hatası ayrıntıları

429 HTTP durumu genellikle bir isteği reddetmek için kullanılır çünkü çağrı hızı sınırına ulaşıldı. Işlem kaynak sağlayıcısından tipik bir azaltma hatası yanıtı aşağıdaki örneğe benzer olacaktır (yalnızca ilgili üstbilgiler gösterilir):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

0 kalan çağrı sayısına sahip ilke, azaltma hatasının döndürüldüğü bir hata nedeniyle belirlenir. Bu durumda, `HighCostGet30Min`. Yanıt gövdesinin genel biçimi Genel Azure Resource Manager API hata biçimidir (OData ile uyumlu). Ana hata kodu `OperationNotAllowed`, bir hesaplama kaynak sağlayıcısının, azaltma hatalarını (diğer istemci hatalarının türleri arasında) raporlamak için kullandığı bir işlem kaynağı sağlayıcısıdır. İç hata (ler) i özelliği,azaltmaihlalininayrıntılarınasahipserileştirilmişbirJSONyapısıiçerir.`message`

Yukarıda gösterildiği gibi, her azaltma hatası, istemcinin `Retry-After` isteği yeniden denemeden önce bekleyeceği en az saniye sayısını sağlayan üstbilgiyi içerir. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API çağrı hızı ve azaltma hatası Çözümleyicisi
Işlem Kaynak sağlayıcısının API 'SI için bir sorun giderme özelliğinin önizleme sürümü kullanılabilir. Bu PowerShell cmdlet 'leri, işlem grubu (ilke) başına işlem ve kısıtlama ihlalleri başına API isteği hızı hakkında istatistikler sağlar:
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-Azloganaliz Tickısıt/Istek](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

API çağrısı istatistikleri, bir aboneliğin istemcilerinin davranışına yönelik harika öngörüler sağlayabilir ve azaltmasına neden olan çağrı desenlerinin kolay tanımlanmasını sağlar.

Çözümleyici 'nin bir sınırlaması, disk ve anlık görüntü kaynak türleri için istekleri saymayan bir kısıtlamadır (yönetilen diskleri desteklemek için). CRP 'nin telemetrisinden veri topladığı için, ARM 'de azaltma hatalarını tanımlamaya de yardımcı olamaz. Ancak bunlar, daha önce anlatıldığı gibi, ayırt edici yanıt üst bilgilerine göre kolayca belirlenebilir.

PowerShell cmdlet 'leri, doğrudan istemciler tarafından kolayca çağrılabilen (ancak henüz resmi olmayan destek olmadan) bir REST hizmeti API 'SI kullanıyor. HTTP istek biçimini görmek için cmdlet 'leri-hata ayıklama anahtarı veya gözetini Fiddler ile Yürütmeyle çalıştırın.


## <a name="best-practices"></a>En iyi uygulamalar 

- Azure hizmeti API hatalarını koşulsuz ve/veya hemen tekrar denemeyin. Ortak bir oluşum, istemci kodunun yeniden denenmeyecek bir hatayla karşılaşıldığında hızlı bir yeniden deneme döngüsüne ulaşmak içindir. Yeniden denemeler, hedef işlemin grubu için izin verilen çağrı sınırını en sonunda ve aboneliğin diğer istemcilerine etkiler. 
- Yüksek hacimli API Otomasyonu durumlarında, bir hedef işlem grubu için kullanılabilir çağrı sayısı düşük eşiğin altına düşerse, öngörülebilir istemci tarafı kendi kendine kısıtlama uygulamayı düşünün. 
- Zaman uyumsuz işlemleri izlerken, yeniden deneme üst bilgisi ipuçlarına uyar. 
- İstemci kodunda belirli bir sanal makine hakkında bilgi gerekiyorsa, bu VM 'yi, kapsayan kaynak grubundaki tüm VM 'Leri veya tüm aboneliğin listesini, sonra da istemci tarafında gerekli VM 'yi seçerek doğrudan sorgulayın. 
- İstemci kodu belirli bir Azure konumundan sanal makinelere, disklere ve anlık görüntülere ihtiyaç duyuyorsa, tüm abonelik sanal makinelerini sorgulamak ve sonra istemci tarafında konuma göre filtrelemek yerine sorgunun konum tabanlı biçimini kullanın: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` işlem kaynak sağlayıcısı bölgesel olarak sorgulama noktalarının. 
-   Belirli VM 'Lerde ve sanal makine ölçek kümelerinde API kaynaklarını oluştururken veya güncelleştirirken, döndürülen zaman uyumsuz işlemi kaynak URL 'sinin kendisi üzerinde yoklama gerçekleştirenden (öğesine göre `provisioningState`) izlemek çok daha etkilidir.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'daki diğer hizmetlere yönelik yeniden deneme Kılavuzu hakkında daha fazla bilgi için bkz. [belirli hizmetler Için yeniden deneme Kılavuzu](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)

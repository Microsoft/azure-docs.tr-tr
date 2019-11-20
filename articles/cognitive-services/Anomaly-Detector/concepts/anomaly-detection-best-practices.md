---
title: Anomali Algılayıcısı API'sini kullanırken en iyi yöntemler
titleSuffix: Azure Cognitive Services
description: En iyi yöntemler hakkında Anomali algılayıcısı API'siyle anomalileri tespit edilirken öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721630"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Anomali algılayıcısı API kullanımı için en iyi uygulamalar

Anomali algılayıcısı API'si, bir durum bilgisi olmayan anomali algılama hizmetidir. Doğruluk ve performans sonuçlarını, tarafından etkilenebilir:

* Zaman serisi verilerinin nasıl hazırlanır.
* Kullanılan Anomali algılayıcısı API parametreler.
* Veri noktası, API isteği sayısı. 

Verileriniz için en iyi sonuçları elde API kullanımı için en iyi uygulamalar hakkında bilgi edinmek için bu makaleyi kullanın. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Batch (Tümü) veya en son ne zaman kullanılacağını (son), anomali algılama noktası

Anomali algılayıcısı API'nin batch algılama uç noktası aracılığıyla tüm zaman serisi verilerini anormallikleri algılamanızı sağlar. Bu algılama modunda, tek bir istatistik model oluşturulur ve veri kümesi her noktasında uygulanır. Zaman serisi varsa özellikleri, bir API çağrısında, verilerin önizlemesini görmek için batch algılama kullanılması önerilir.

* Bazen anomalileri ile dönemsel zaman serisi.
* Ara sıra ani/DIP ile bir düz eğilim zaman serisi. 

İzleme ya da özelliklere sahip olmayan bir zaman serisi verilerinde kullanarak gerçek zamanlı veriler için anomali algılama toplu kullanımı önerilmemektedir. 

* Batch algılama oluşturur ve yalnızca bir modeli uygular, algılama her nokta için tüm dizileri bağlamında gerçekleştirilir. Zaman serisi verileri eğilimleri yukarı ve aşağı mevsimsellik, bazı noktalarını olmadan (dıps ve veri artış) değiştirirseniz modeli tarafından eksik olabilir. Benzer şekilde, daha az önemli olanları veri kümesindeki veriler daha sonra daha bazı noktalar değişikliğinin modele birleştirilmesi yeterince önemli olarak sayılması değil.

* Batch algılama, gerçek zamanlı verileri izleme, çözümlenen noktalarının sayısı nedeniyle yaparken en son noktası durumunu anomali algılama daha yavaştır.

Gerçek zamanlı verileri izleme için en son veri noktanız yalnızca anomali durumunu algılama öneririz. Sürekli olarak en son noktası algılama uygulayarak, izleme verilerini akış daha verimli ve doğru bir şekilde gerçekleştirebilirsiniz.

Aşağıdaki örnekte, bu algılama modları performansı olabilir gerçekleşen etkiyi açıklamaktadır. İlk resim anomali durumu son noktası 28 önceden görülen veri noktaları boyunca sürekli olarak algılanıyor sonucunu gösterir. Kırmızı noktalar anomalileri ' dir.

![En son noktası kullanarak anomali algılama gösteren görüntü](../media/last.png)

Batch anomali algılama özelliğiyle aynı veri kümesi aşağıdadır. İşlem için oluşturulan model dikdörtgen tarafından işaretlenen birkaç anomalileri yoksaydı.

![Anomali algılama batch yöntemi kullanılarak gösteren görüntü](../media/entire.png)

## <a name="data-preparation"></a>Veri hazırlama

Zaman serisi Anomali algılayıcısı API kabul biçimlendirilmiş JSON istek nesnesine verileri. Zaman serisi sırayla zaman içinde kayıtlı olan herhangi bir sayısal veri olabilir. Zaman serisi verilerinin windows API'SİNİN performansını artırmak için Anomali algılayıcısı API uç noktası için gönderebilirsiniz. En az sayıda veri noktası gönderebilirsiniz 12, ve en fazla 8640 noktaları. [Ayrıntı düzeyi](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) verilerinizi, örnek oranı olarak tanımlanır. 

Veri noktaları Anomali algılayıcısı API'ye gönderilen geçerli Eşgüdümlü Evrensel Saat (UTC) zaman damgası ve sayısal değer olmalıdır. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Verilerinizi bir standart olmayan bir zaman aralığında örneklenir, onu ekleyerek belirtebilirsiniz `customInterval` isteğinizdeki özniteliği. Örneğin, her 5 dakikada bir örneklenir seri, aşağıdaki JSON isteğiniz ekleyebilirsiniz:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Veri noktaları

Veri noktaları eşit olarak dağıtılmış bir zaman serisi veri kümesi, ince bir ayrıntı düzeyi (küçük bir örnekleme aralığı. özellikle ettiklerinizi ortak For example, veri birkaç dakikada bir örneklenir). Daha az % 10 beklenen veri noktası sayısı eksik algılama sonuçlarınız üzerinde olumsuz bir etkiye sahip olmamalıdır. Özelliklerini veri noktalarından bir önceki süre, doğrusal enterpolasyon veya hareketli ortalama değiştirme gibi temel veri boşlukları doldurma göz önünde bulundurun.

### <a name="aggregate-distributed-data"></a>Dağıtılmış veri toplama

Anomali algılayıcısı API bir eşit olarak dağıtılmış zaman serisinde bulunan en iyi şekilde çalışır. Verilerinizi rastgele dağıtılırsa, bu dakikalık, saatlik veya günlük gibi süre birimi örneğin toplama ölçütü.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Dönemsel desenlerine sahip veriler üzerinde anomali algılama

Zaman serisi verilerinizle dönemsel desen (bir düzenli aralıklarla gerçekleşir) olduğunu biliyorsanız, doğruluk ve API yanıt süresini artırabilir. 

Belirten bir `period` oluşturduğunuzda, JSON isteğiniz kadar % 50'ye anomali algılama gecikmeyi azaltabilir. `period` Desen yinelemek için kaç kabaca veri noktaları zaman serisi belirten bir tamsayı gerçekleştirir. Örneğin, bir zaman serisi ile günlük bir veri noktası gerekir bir `period` olarak `7`, ve bir zaman serisi (ile aynı haftalık deseni) saatte bir nokta ile olması gereken bir `period` , `7*24`. Veri desenlerini emin değilseniz, bu parametre belirtmeniz gerekmez.

En iyi sonuçlar için 4 sağlamak `period`kullanıcının veri noktası yanı sıra, ek bir değerinde. Örneğin, haftalık desenle saatlik veri yukarıda açıklandığı gibi istek gövdesindeki 673 veri noktası sağlamanız gerekir (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Gerçek zamanlı izleme için örnekleme verileri

Akış verilerinizi bir kısa aralıklarla (örneğin, saniyeler veya dakikalar içinde) örneklenir, önerilen veri noktalarının sayısını gönderme Anomali algılayıcısı API'nin en fazla izin verilen (8640 veri noktası sayısıdır) aşabilir. Verilerinizi bir kararlı dönemsel deseni gösteriyorsa, saat gibi daha büyük bir zaman aralığı, zaman serisi verilerinin bir örnek gönderme göz önünde bulundurun. Bu şekilde, verilerinizi örnekleme, API yanıt süresi de fark edilir derecede artırabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Anomali algılayıcısı API nedir?](../overview.md)
* [Hızlı Başlangıç: Anomali algılayıcısı REST API kullanarak, zaman serisi verilerinde görülen anomalileri algılayın](../quickstarts/detect-data-anomalies-csharp.md)

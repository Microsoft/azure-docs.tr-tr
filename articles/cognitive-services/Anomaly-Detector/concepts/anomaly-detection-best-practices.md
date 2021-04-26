---
title: Anomali Algılayıcısı API'sini kullanırken en iyi yöntemler
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcı API 'SI ile anomali algılama konusunda en iyi yöntemler hakkında bilgi edinin.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: mbullwin
ms.openlocfilehash: 43ccde054a9630b251aa6c206028d29c7c699316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936202"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Anomali algılayıcı API 'sini kullanmaya yönelik en iyi uygulamalar

Anomali algılayıcı API 'SI, durum bilgisiz bir anomali algılama hizmetidir. Sonuçlarının doğruluğu ve performansı şu şekilde etkilenebilir:

* Zaman serisi verileriniz nasıl hazırlanır.
* Kullanılan anomali algılayıcısı API parametreleri.
* API isteğinizin veri noktası sayısı. 

Verilerinize ilişkin en iyi sonuçları elde etmek için API 'YI kullanmaya yönelik en iyi yöntemler hakkında bilgi edinmek için bu makaleyi kullanın. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Batch (tamamının) veya en son (son) nokta anomali algılama ne zaman kullanılır

Anomali algılayıcı API 'sinin toplu iş algılama uç noktası, tüm zaman serisi verileriniz sayesinde anormallikleri tespit etmenizi sağlar. Bu algılama modunda, tek bir istatistiksel model oluşturulur ve veri kümesindeki her bir noktaya uygulanır. Zaman seriniz aşağıdaki özelliklere sahipse, verileri tek bir API çağrısında önizlemek için Batch algılamayı kullanmanızı öneririz.

* Anormal bir zaman serisi, zaman zaman anormalileri.
* Düz bir eğilim süresi serisi, zaman zaman ani artışlar/DIB 'ler. 

Gerçek zamanlı veri izleme için Batch anomali algılama özelliğinin kullanılması veya yukarıdaki özelliklere sahip olmayan zaman serisi verilerinde kullanılması önerilmez. 

* Toplu iş algılama yalnızca bir model oluşturur ve uygular, her bir noktanın algılanması tüm serinin bağlamında yapılır. Zaman serisi verileri mevsimsellik olmadan yukarı ve aşağı doğru eğilimler, model tarafından bazı değişiklik noktaları (veri içindeki artışlar ve ani artışlar) kaçırılmış olabilir. Benzer şekilde, veri kümesinde daha sonra daha fazla önemli olan bazı değişiklik noktaları, modele dahil edilecek kadar önemli olarak sayılmayabilir.

* Toplu algılama, çözümlenmekte olan nokta sayısı nedeniyle gerçek zamanlı veri izleme yaparken en son noktanın anomali durumunu algılamada daha yavaştır.

Gerçek zamanlı veri izleme için, yalnızca en son veri noktasının anomali durumunu tespit etmenizi öneririz. En son nokta algılamayı sürekli uygulayarak, akış veri izleme daha etkili ve doğru şekilde yapılabilir.

Aşağıdaki örnekte, bu algılama modlarının performansa sahip olabileceği etkisi açıklanmaktadır. İlk resimde, daha önce görülmüş olan veri noktalarında, anomali durum en son noktasını sürekli algılama sonucu gösterilmektedir. Kırmızı köşeler bozukluklardır.

![En son noktayı kullanarak anomali algılamayı gösteren resim](../media/last.png)

Batch anomali algılama kullanılarak aynı veri kümesi aşağıda verilmiştir. İşlem için oluşturulan model, dikdörtgenlerle işaretlenmiş birkaç aykırı yoksaydı.

![Batch metodunu kullanarak anomali algılamayı gösteren resim](../media/entire.png)

## <a name="data-preparation"></a>Veri hazırlama

Anomali algılayıcı API 'SI, bir JSON istek nesnesi halinde biçimlendirilen zaman serisi verilerini kabul eder. Zaman serisi, zaman içinde sıralı olarak kaydedilmiş herhangi bir sayısal veri olabilir. API 'nin performansını geliştirmek için zaman serisi verilerinizin pencerelerini anomali algılayıcı API uç noktasına gönderebilirsiniz. Gönderebilmeniz gereken en az veri noktası sayısı 12, üst sınır 8640 puntodur. [Ayrıntı düzeyi](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) , verilerinizin örneklendiği hız olarak tanımlanmıştır. 

Anomali algılayıcı API 'sine gönderilen veri noktaları, geçerli bir Eşgüdümlü Evrensel Saat (UTC) Zaman damgasına ve sayısal bir değere sahip olmalıdır. 

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

Verileriniz standart olmayan bir zaman aralığında örneklenir, isteğinize özniteliği ekleyerek bunu belirtebilirsiniz `customInterval` . Örneğin, seriniz her 5 dakikada bir örneklenir, JSON isteğinize aşağıdakileri ekleyebilirsiniz:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Eksik veri noktaları

Eksik veri noktaları, özellikle ince ayrıntı düzeyi (küçük örnekleme aralığı) olan eşit oranda dağıtılmış zaman serisi veri kümelerinde ortaktır. Örneğin, veriler her birkaç dakikada örneklenir. Verilerinizde beklenen sayıda noktaya ait %10 ' dan az olması, algılama sonuçlarınızda olumsuz bir etkiye sahip olmamalıdır. Verilerdeki boşlukları, daha önceki bir dönemden, doğrusal ilişkilendirmeden veya hareketli bir ortadaki veri noktalarını değiştirme gibi özelliklerine göre doldurmayı göz önünde bulundurun.

### <a name="aggregate-distributed-data"></a>Dağıtılmış verileri toplama

Anomali algılayıcı API 'SI, eşit olarak dağıtılan bir zaman serisinde en iyi şekilde çalışmaktadır. Verileriniz rastgele dağıtılmışsa, bu süreyi dakika başına, saatlik veya günlük gibi bir zaman birimi ile toplamalısınız.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Dönemsel desenlerle verilerde anomali algılama

Zaman serisi verilerinizin düzenli aralıklarla bir düzene sahip olduğunu biliyorsanız, doğruluk ve API yanıt süresini geliştirebilirsiniz. 

`period`JSON isteğinizi oluştururken belirtme, anomali algılama gecikmesini %50 oranında azaltabilir. , `period` Zaman serisinin bir kalıbı yinelemek için kaç veri noktası olduğunu kabaca belirten bir tamsayıdır. Örneğin, günlük bir veri noktasına sahip bir zaman serisi bir `period` olarak olur `7` ve saat başına bir noktaya sahip bir zaman serisi (aynı haftalık desenli) ' a sahip olur `period`  `7*24` . Verilerinizin desenlerinden emin değilseniz, bu parametreyi belirtmeniz gerekmez.

En iyi sonuçlar için, üç ve daha `period` fazla veri noktasına ek bir tane girin. Örneğin, yukarıda açıklanan haftalık bir desenli saatlik veriler, istek gövdesinde () 673 veri noktası sağlamalıdır `7 * 24 * 4 + 1` .

### <a name="sampling-data-for-real-time-monitoring"></a>Gerçek zamanlı izleme için örnekleme verileri

Akış verileriniz kısa bir aralıkta (örneğin, saniyeler veya dakikalar) örneklense, önerilen sayıda veri noktası gönderilmesi anomali algılayıcı API 'sinin izin verilen en fazla sayıyı (8640 veri noktası) aşabilir. Verileriniz kararlı bir dönemsel model gösteriyorsa, zaman serisi verilerinizin bir örneğini saat gibi daha büyük bir zaman aralığında göndermeyi göz önünde bulundurun. Verilerinizi bu şekilde örnekleme, API yanıt süresini de önemli ölçüde artırır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Anomali Algılayıcısı API'si nedir?](../overview.md)
* [Hızlı başlangıç: anomali algılayıcısının kullanıldığı zaman serisi verilerinizde bozukluklar saptama](../quickstarts/client-libraries.md)

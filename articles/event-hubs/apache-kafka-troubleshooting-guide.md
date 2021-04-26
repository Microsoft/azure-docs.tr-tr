---
title: Apache Kafka için Azure Event Hubs sorunlarını giderme
description: Bu makalede Apache Kafka için Azure Event Hubs ile ilgili sorunların nasıl giderileceği gösterilmektedir
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a9d4a93f0074f206cd4627913505c66eb6480cbd
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314091"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Event Hubs için sorun giderme kılavuzu Apache Kafka
Bu makalede, Apache Kafka için Event Hubs kullanırken çalıştırabileceğiniz sorunlar için sorun giderme ipuçları sunulmaktadır. 

## <a name="server-busy-exception"></a>Sunucu meşgul özel durumu
Kafka azaltma nedeniyle sunucu meşgul özel durumu alabilirsiniz. AMQP istemcileri sayesinde, Event Hubs hemen hizmet azaltmaya göre **sunucu meşgul** özel durumu döndürür. "Daha sonra yeniden dene" iletisiyle eşdeğerdir. Kafka ' de iletiler tamamlanmadan önce gecikiyor. Gecikme uzunluğu, `throttle_time_ms` Üretim/getirme yanıtında milisaniye cinsinden döndürülür. Çoğu durumda, bu geciktirilen istekler Event Hubs panolar üzerinde sunucu meşgul özel durumları olarak günlüğe kaydedilmez. Bunun yerine, yanıtın `throttle_time_ms` değeri, üretilen işin sağlanan kotayı aştığı bir gösterge olarak kullanılmalıdır.

Trafik aşırı ise, hizmet aşağıdaki davranışa sahiptir:

- Bir isteğin gecikmesi, istek zaman aşımını aşarsa, Event Hubs **Ilke ihlali** hata kodu döndürür.
- Getirme isteğinin gecikmesi istek zaman aşımını aşarsa, Event Hubs isteği kısıtlanacak şekilde günlüğe kaydeder ve boş kayıt kümesiyle yanıt verir ve hata kodu yoktur.

[Adanmış kümeler](event-hubs-dedicated-overview.md) kısıtlama mekanizmalarına sahip değildir. Tüm küme kaynaklarınızı kullanmaya ücretsiz olursunuz.

## <a name="no-records-received"></a>Hiçbir kayıt alınmadı
Tüketicileri hiçbir kayıt almaz ve sürekli yeniden dengelemeden karşılaşabilirsiniz. Bu senaryoda, tüketiciler hiçbir kayıt almaz ve sürekli yeniden dengeleyin. Bu durumda özel durum veya hata yoktur, ancak Kafka Günlükler tüketicilerinin gruba yeniden bağlanmaya ve bölüm atamaya çalışırken takıldığını gösterir. Olası birkaç neden vardır:

- `request.timeout.ms`En azından 60000 ' un önerilen değeri olan ve `session.timeout.ms` en azından 30000 ' nin önerilen değeri olduğundan emin olun. Bu ayarların çok düşük olması, müşteri zaman aşımları oluşmasına neden olabilir. Bu, daha sonra yeniden dengelemeye neden olur (daha sonra daha fazla zaman aşımı oluşmasına neden olur ve bu da daha fazla zaman 
- Yapılandırmanız önerilen değerlerle eşleşiyorsa ve yine de sabit yeniden dengeleme görmeye devam ediyorsanız, bir sorun açmaya (hata ayıklamaya yardımcı olabilmemiz için tüm yapılandırmanızı eklediğinizden emin olun)!

## <a name="compressionmessage-format-version-issue"></a>Sıkıştırma/Ileti biçimi sürüm sorunu
Kafka sıkıştırmayı destekler ve şu anda Kafka için Event Hubs. Bir ileti biçimi sürümü (örneğin,) ile ilgili hatalar `The message format version on the broker does not support the request.` bir istemci, aracılarımıza sıkıştırılmış Kafka iletileri göndermeyi denediğinde oluşur.

Sıkıştırılmış veriler gerekliyse, aracılarla gönderilmeden önce verilerinizi sıkıştırmak ve aldıktan sonra dosyayı sıkıştırmak geçerli bir geçici çözüm olur. İleti gövdesi yalnızca hizmete yönelik bir bayt dizisidir, bu nedenle istemci tarafı sıkıştırma/açma herhangi bir soruna neden olmaz.

## <a name="unknownserverexception"></a>UnknownServerException
Kafka istemci kitaplıklarından aşağıdaki örneğe benzer bir UnknownServerException alabilirsiniz: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Microsoft desteği ile bir bilet açın.  Hata ayıklama için, UTC 'de hata ayıklama düzeyinde günlüğe kaydetme ve özel durum zaman damgaları yararlı olur. 

## <a name="other-issues"></a>Diğer sorunlar
Event Hubs üzerinde Kafka kullanırken sorunlarla karşılaşırsanız aşağıdaki öğeleri kontrol edin.

- **Güvenlik duvarı engelleme trafiği** -güvenlik duvarınız tarafından **9093** numaralı bağlantı noktasının engellenmediğinden emin olun.
- **Topıauthorizationexception** -bu özel durumun en yaygın nedenleri şunlardır:
    - Yapılandırma dosyanızdaki bağlantı dizesinde bir yazım hatası veya
    - Temel bir katman ad alanı üzerinde Kafka için Event Hubs kullanılmaya çalışılıyor. Kafka özelliği için Event Hubs [yalnızca standart ve adanmış katman ad alanları için desteklenir](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Kafka sürüm uyuşmazlığı** -Kafka ekosistemleri Için Event Hubs Kafka 1,0 sürümlerini ve üstünü destekler. Kafka sürüm 0,10 ve üstünü kullanan bazı uygulamalar, Kafka protokolünün geriye dönük uyumluluğu nedeniyle zaman zaman çalışabilir, ancak eski API sürümlerinin kullanılması önemle önerilir. Kafka sürümleri 0,9 ve öncesi, gerekli SASL protokollerini desteklemez ve Event Hubs bağlanamaz.
- **Kafka ile KULLANıLıRKEN AMQP üst bilgilerinde garip kodlamalar** , AMQP üzerinden olay hub 'ına olay gönderirken AMQP Encoding içinde herhangi bir AMQP yük üst bilgisi serileştirilir. Kafka tüketicileri AMQP 'deki üstbilgilerin serisini kaldıramıyor. Üst bilgi değerlerini okumak için AMQP üstbilgilerinin el ile kodunu çözün. Alternatif olarak, Kafka protokolü aracılığıyla kullanacağınızı biliyorsanız AMQP üst bilgilerini kullanmaktan kaçınabilirsiniz. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)bakın.
- **SASL kimlik doğrulaması** -Event Hubs için gereken SASL kimlik doğrulama protokolü ile işbirliği yapmak, bu gözle buluşmamak daha zor olabilir. SASL kimlik doğrulamasında Framework 'ün kaynaklarını kullanarak yapılandırmanın sorunlarını giderebileceğinizden bkz.. 

## <a name="limits"></a>Sınırlar
Apache Kafka vs. Event Hubs Kafka. Çoğu bölümde, Azure Event Hubs ' Kafka arabirimi aynı varsayılanlara, özelliklere, hata kodlarına ve Apache Kafka genel davranışına sahiptir. Bu iki açıkça farklı olan örnekler (veya Event Hubs Kafka olmayan bir sınır uygular) aşağıda listelenmiştir:

- Özelliğin uzunluk üst sınırı `group.id` 256 karakterdir
- En büyük boyut `offset.metadata.max.bytes` 1024 bayttır
- En büyük iç günlük boyutu 1 MB olan, konum işleme/bölüm başına 4 çağrı/saniye azaltılır


## <a name="next-steps"></a>Sonraki adımlar
Kafka için Event Hubs ve Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:  

- [Event Hubs için Apache Kafka Geliştirici Kılavuzu](apache-kafka-developer-guide.md)
- [Event Hubs için Apache Kafka geçiş kılavuzu](apache-kafka-migration-guide.md)
- [Sık Sorulan Sorular-Apache Kafka için Event Hubs](apache-kafka-frequently-asked-questions.yml)
- [Önerilen yapılandırma](apache-kafka-configurations.md)

---
title: Azure Işlevlerinde olay odaklı ölçeklendirme
description: Tüketim planı ve Premium plan işlev uygulamalarının ölçeklendirme davranışlarını açıklar.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937754"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Azure Işlevlerinde olay odaklı ölçeklendirme

Tüketim ve Premium planlarında Azure Işlevleri, Işlevler ana bilgisayarının ek örneklerini ekleyerek CPU ve bellek kaynaklarını ölçeklendirir. Örnek sayısı, bir işlevi tetikleyen olay sayısına göre belirlenir. 

Tüketim planında Işlevlerin ana bilgisayarının her örneği 1,5 GB bellek ve bir CPU ile sınırlıdır.  Ana bilgisayarın bir örneği, bir işlev uygulamasının tüm işlevleri bir örnek içinde kaynak paylaşır ve aynı anda ölçeklendirin. Aynı tüketim planı ölçeğini bağımsız olarak paylaşan işlev uygulamaları.  Premium planda, plan boyutu o örnekteki plandaki tüm uygulamalar için kullanılabilir belleği ve CPU 'YU belirler.  

İşlev kodu dosyaları, işlevin ana depolama hesabındaki Azure dosya paylaşımlarında depolanır. İşlev uygulamasının ana depolama hesabını sildiğinizde, işlev kodu dosyaları silinir ve kurtarılamaz.

## <a name="runtime-scaling"></a>Çalışma zamanı ölçeklendirme

Azure Işlevleri, olayların oranını izlemek ve ölçeğini genişletmek veya ölçeklendirmek için *Ölçek denetleyicisi* adlı bir bileşen kullanır. Ölçek denetleyicisi her tetikleyici türü için buluşsal yöntemler kullanır. Örneğin, bir Azure kuyruk depolama tetikleyicisi kullanırken, sıra uzunluğuna ve en eski sıra iletisinin yaşa göre ölçeklendirilir.

Azure Işlevleri için ölçek birimi, işlev uygulamasıdır. İşlev uygulaması ölçeklenirse, Azure Işlevleri ana bilgisayarının birden çok örneğini çalıştırmak için ek kaynaklar ayrılır. Buna karşılık, işlem talebi azaltıldı, ölçek denetleyicisi işlev ana bilgisayar örneklerini kaldırır. İşlev uygulaması içinde hiçbir işlev çalışmadığı zaman örneklerin sayısı sonunda "olarak ölçeklendirilir".

![Denetleyici izleme olaylarını ölçeklendirme ve örnek oluşturma](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Soğuk başlangıç

İşlev uygulamanız birkaç dakika boyunca boşta kaldıktan sonra, Platform, uygulamanızın çalıştırıldığı örneklerin sayısını sıfıra ölçeklendirebilir. Sonraki istek, sıfırdan diğerine ölçeklendirmeye yönelik ek gecikme süresine sahiptir. Bu gecikme süresi _soğuk başlangıç_ olarak adlandırılır. İşlev uygulamanız için gereken bağımlılıkların sayısı, soğuk başlangıç süresini etkileyebilir. Soğuk başlatma, yanıt döndürmesi gereken HTTP Tetikleyicileri gibi zaman uyumlu işlemler için bir sorundur. Soğuk başlıyorsa işlevlerinizi etkilese, **her zaman açık** ayarı etkin olan bir Premium planda veya adanmış bir planda çalışmayı düşünün.   

## <a name="understanding-scaling-behaviors"></a>Ölçeklendirme davranışlarını anlama

Ölçeklendirme, bir dizi etkene göre farklılık gösterebilir ve seçilen tetikleyici ve dile göre farklı şekilde ölçeklendirebilir. ' Nin farkında olması için ölçeklendirmenin bazı çok karmaşık özellikleri vardır:

* **En fazla örnek:** Tek bir işlev uygulaması yalnızca en fazla 200 örneğe ölçeklendirir. Tek bir örnek aynı anda birden fazla ileti veya isteği işleyebilir, bu nedenle eşzamanlı yürütmeler sayısında bir küme sınırı yoktur.  Ölçeği azaltmak için [daha düşük bir değer belirtebilirsiniz](#limit-scale-out) .
* **Yeni örnek oranı:** HTTP Tetikleyicileri için, saniyede en çok bir kez yeni örnekler ayrılır. HTTP olmayan Tetikleyiciler için, en çok, her 30 saniyede bir yeni örnek ayrılır. Bir [Premium planda](functions-premium-plan.md)çalışırken ölçekleme daha hızlıdır.
* **Ölçek verimliliği:** Service Bus Tetikleyiciler için, en verimli ölçekleme için kaynaklardaki hakları _Yönet_ ' i kullanın. _Dinleme_ haklarıyla, ölçek kararlarını bilgilendirmek için sıra uzunluğu kullanılamadığından ölçekleme doğru değildir. Service Bus erişim ilkelerinde hakları ayarlama hakkında daha fazla bilgi edinmek için bkz. [paylaşılan erişim yetkilendirme ilkesi](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Olay Hub 'ı Tetikleyicileri için başvuru makalesindeki [ölçeklendirme Kılavuzu](functions-bindings-event-hubs-trigger.md#scaling) ' na bakın. 

## <a name="limit-scale-out"></a>Ölçeği genişletme sınırı

Bir uygulamanın ölçeğini genişletmek için kullanılan en fazla örnek sayısını kısıtlamak isteyebilirsiniz.  Bu, bir veritabanı gibi bir aşağı akış bileşeninin aktarım hızını sınırlı olduğu durumlarda yaygın olarak kullanılır.  Varsayılan olarak, tüketim planı işlevleri 200 örneğe kadar ölçeği ölçeklendirir ve Premium plan işlevleri, en fazla 100 örneğe kadar ölçeklendirecektir.  Değeri değiştirerek belirli bir uygulama için daha düşük bir en yüksek değer belirtebilirsiniz `functionAppScaleLimit` .  , `functionAppScaleLimit` `0` `null` Kısıtlanmamış veya sınırsız için ya da en fazla uygulama arasında geçerli bir değer ayarlanabilir `1` .

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Ölçeklenebilir uygulamalar için en iyi uygulamalar ve desenler

Konak yapılandırması, çalışma zamanı kaplama ve kaynak verimliliği dahil olmak üzere, bir işlev uygulamasının ölçeklendirme biçimini etkileyen birçok yönü vardır.  Daha fazla bilgi için [performans konuları makalesinin ölçeklenebilirlik bölümüne](functions-best-practices.md#scalability-best-practices)bakın. Ayrıca, işlev uygulamanız ölçeklenirken bağlantıların nasıl davranacağını de bilmelisiniz. Daha fazla bilgi için bkz. [Azure işlevlerinde bağlantıları yönetme](manage-connections.md).

Python ve Node.js ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure Işlevleri Python Geliştirici Kılavuzu-ölçeklendirme ve eşzamanlılık](functions-reference-python.md#scaling-and-performance) ve [Azure işlevleri Node.js Geliştirici Kılavuzu-ölçeklendirme ve eşzamanlılık](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Faturalama modeli

Farklı planların [faturalandırılması, Azure işlevleri fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/functions/)ayrıntılı olarak açıklanmıştır. Kullanım işlevi uygulama düzeyinde toplanır ve yalnızca işlev kodunun yürütüldüğü süreyi sayar. Faturalandırma için birimler aşağıda verilmiştir:

* **Gigabayt-saniye cinsinden kaynak tüketimi (GB-s)**. Bir işlev uygulaması içindeki tüm işlevler için bellek boyutu ve yürütme süresinin birleşimi olarak hesaplanır. 
* **Yürütmeler**. Bir olay tetikleyicisine yanıt olarak bir işlev yürütüldüğünde her seferinde sayılır.

Tüketim faturanızı nasıl anlayacağınızı öğrenmek için faydalı sorgular ve bilgiler [faturalandırma hakkında SSS bölümünde](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)bulunabilir.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Işlevleri barındırma seçenekleri](functions-scale.md)


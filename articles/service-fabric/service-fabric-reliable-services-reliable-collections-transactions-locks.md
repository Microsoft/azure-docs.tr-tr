---
title: Güvenilir koleksiyonlardaki işlemler ve kilit modları
description: Azure Service Fabric güvenilir durum Yöneticisi ve güvenilir koleksiyonlar Işlemleri ve kilitleme.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 5d2cbb517ea5ca45697cd9124b82e9ef13dd32db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784351"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric güvenilir koleksiyonlardaki işlemler ve kilit modları

## <a name="transaction"></a>İşlem

İşlem, tek bir mantıksal iş birimi olarak gerçekleştirilen işlemlerin sırasıdır. Veritabanı işlemlerinin ortak [ACID](https://en.wikipedia.org/wiki/ACID) (*kararlılık*, *tutarlılık*, *yalıtım*, *dayanıklılık*) özelliklerini sergiler:

* **Atomicity**: bir işlem atomik iş birimi olmalıdır. Diğer bir deyişle, tüm veri değişiklikleri gerçekleştirilir veya hiçbir şey yapılmaz.
* **Tutarlılık**: tamamlandığında, bir işlem tüm verileri tutarlı bir durumda bırakmalıdır. Tüm iç veri yapıları işlemin sonunda doğru olmalıdır.
* **Yalıtım**: eşzamanlı işlemler tarafından yapılan değişiklikler, diğer eş zamanlı işlemler tarafından yapılan değişikliklerden yalıtılmalıdır. Bir [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction) içindeki bir işlem için kullanılan yalıtım düzeyi, Işlemi gerçekleştiren [ıreliablestate](/dotnet/api/microsoft.servicefabric.data.ireliablestate) tarafından belirlenir.
* **Dayanıklılık**: bir işlem tamamlandıktan sonra, etkileri sistemde kalıcı olarak gerçekleştirilir. Değişiklikler, sistem hatası durumunda bile devam ediyor.

### <a name="isolation-levels"></a>Yalıtım düzeyleri

Yalıtım düzeyi, işlemin diğer işlemler tarafından yapılan değişikliklerden yalıtılması gereken dereceyi tanımlar.
Güvenilir koleksiyonlarda desteklenen iki yalıtım düzeyi vardır:

* **Yinelenebilir okuma**: Bu deyimlerin, başka işlemler tarafından değiştirilmiş ancak henüz onaylanmamış verileri okuyamadığından ve geçerli işlem bitene kadar geçerli işlem tarafından okunan verileri değiştiremediğini belirtir.
* **Anlık görüntü**: bir işlemdeki tüm deyimden okunan verilerin, işlemin başlangıcında varolan verilerin işlemsel olarak tutarlı bir sürümüdür olduğunu belirtir.
  İşlem yalnızca işlemin başlangıcından önce kaydedilmiş olan veri değişikliklerini tanıyabilir.
  Geçerli işlemin başlangıcından sonra gerçekleştirilen diğer işlemler tarafından yapılan veri değişiklikleri, geçerli işlemde yürütülen deyimlere görünür değildir.
  Efekt, bir işlemdeki deyimler işlemin başlangıcında olduğu gibi yürütülen verilerin anlık görüntüsünü alır.
  Anlık görüntüler, güvenilir koleksiyonlar arasında tutarlıdır.

Güvenilir koleksiyonlar, işleme ve işlemin oluşturulduğu sırada çoğaltmanın rolüne bağlı olarak belirli bir okuma işlemi için kullanılacak yalıtım düzeyini otomatik olarak seçer.
Aşağıda, güvenilir sözlük ve kuyruk işlemleri için yalıtım düzeyi varsayılan değerleri gösteren tablo verilmiştir.

| İşlem \ rol | Birincil | İkincil |
| --- |:--- |:--- |
| Tek bir varlık okuma |Yinelenebilir okuma |Anlık Görüntü |
| Sabit listesi, sayı |Anlık Görüntü |Anlık Görüntü |

> [!NOTE]
> Tek bir varlık Işlemleri için ortak örnekler `IReliableDictionary.TryGetValueAsync` şunlardır `IReliableQueue.TryPeekAsync` .
> 

Hem güvenilir sözlük hem de güvenilir sıra, *yazma bilgilerinizi okumayı* destekler.
Diğer bir deyişle, bir işlem içindeki herhangi bir yazma işlemi, aynı işleme ait olan aşağıdaki okuma için görünür olacaktır.

## <a name="locks"></a>Kilitler

Güvenilir koleksiyonlar bölümünde tüm işlemler, her türlü katı iki aşamalı kilitleme uygular: bir işlem, işlem iptal veya işleme ile sonlanana kadar elde ettiği kilitleri serbest bırakmaz.

Güvenilir sözlük tüm tek varlık işlemleri için satır düzeyinde kilitleme kullanır.
Güvenilir sıra, katı işlem FıFO özelliği için eşzamanlılık kapatır.
Güvenilir sıra, `TryPeekAsync` aynı anda ve/veya ile bir işleme izin veren işlem düzeyinde kilitleri kullanır `TryDequeueAsync` `EnqueueAsync` .
FıFO 'un, `TryPeekAsync` güvenilir sıranın boş olduğunu bir veya `TryDequeueAsync` daha fazla hizmet veriyorsa, bunların de kilitlendiğine göz önünde durun `EnqueueAsync` .

Yazma işlemleri her zaman özel kilitler alır.
Okuma işlemleri için, kilitleme birkaç etkene bağlıdır:

- Anlık görüntü yalıtımı kullanılarak gerçekleştirilen tüm okuma işlemleri kilitleme ücretsizdir.
- Varsayılan olarak herhangi bir yinelenebilir okuma işlemi, paylaşılan kilitleri alır.
- Ancak, yinelenebilir okumayı destekleyen tüm okuma işlemleri için, Kullanıcı paylaşılan kilit yerine bir güncelleştirme kilidi isteyebilir.
Güncelleştirme kilidi, birden çok işlem, olası güncelleştirmeler için kaynakları daha sonra kilitleyen yaygın bir kilitlenme biçimini engellemek için kullanılan asimetrik bir kilitdir.

Kilit uyumluluğu matrisi aşağıdaki tabloda bulunabilir:

| İstek \ verildi | Yok | Paylaşılan | Güncelleştir | Özel |
| --- |:--- |:--- |:--- |:--- |
| Paylaşılan |Çakışma yok |Çakışma yok |Çakışma |Çakışma |
| Güncelleştir |Çakışma yok |Çakışma yok |Çakışma |Çakışma |
| Özel |Çakışma yok |Çakışma |Çakışma |Çakışma |

Güvenilir koleksiyonlar API 'Lerinde zaman aşımı bağımsız değişkeni, kilitlenme algılama için kullanılır.
Örneğin, iki işlem (T1 ve T2) K1 okumayı ve güncelleştirmeyi deniyor.
Her ikisi de paylaşılan kilide bulunduğundan kilitlenmeleri mümkündür.
Bu durumda, işlemlerden biri veya her ikisi zaman aşımına uğrar. Bu senaryoda, bir güncelleştirme kilidi böyle bir kilitlenmeyi önleyebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Güvenilir Koleksiyonlar ile çalışma](service-fabric-work-with-reliable-collections.md)
* [Reliable Services bildirimleri](service-fabric-reliable-services-notifications.md)
* [Reliable Services yedekleme ve geri yükleme (olağanüstü durum kurtarma)](service-fabric-reliable-services-backup-restore.md)
* [Güvenilir durum Yöneticisi yapılandırması](service-fabric-reliable-services-configuration.md)
* [Güvenilir koleksiyonlar için geliştirici başvurusu](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)

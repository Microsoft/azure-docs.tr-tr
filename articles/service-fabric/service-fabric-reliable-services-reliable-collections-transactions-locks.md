---
title: Azure Service Fabric güvenilir koleksiyonlardaki işlemler ve kilit modları | Microsoft Docs
description: Azure Service Fabric güvenilir durum Yöneticisi ve güvenilir koleksiyonlar Işlemleri ve kilitleme.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: atsenthi
ms.openlocfilehash: 8e77e488a3c0a40a714a0e8efffba0a2947454bf
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599320"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric güvenilir koleksiyonlardaki işlemler ve kilit modları

## <a name="transaction"></a>İşlem
İşlem, tek bir mantıksal iş birimi olarak gerçekleştirilen işlemlerin sırasıdır.
Bir işlem aşağıdaki ACID özelliklerini göstermelidir. bakýn https://technet.microsoft.com/library/ms190612)
* **Atomicity**: İşlem atomik bir iş birimi olmalıdır. Diğer bir deyişle, tüm veri değişiklikleri gerçekleştirilir veya hiçbir şey yapılmaz.
* **Tutarlılık**: İşlem tamamlandığında, bir işlemin tüm verileri tutarlı bir durumda bırakması gerekir. Tüm iç veri yapıları işlemin sonunda doğru olmalıdır.
* **Yalıtım**: Eş zamanlı işlemler tarafından yapılan değişiklikler, başka bir eşzamanlı işlem tarafından yapılan değişikliklerden yalıtılmalıdır. Bir ITransaction içindeki bir işlem için kullanılan yalıtım düzeyi, işlemi gerçekleştiren ıreliablestate tarafından belirlenir.
* **Dayanıklılık**: İşlem tamamlandıktan sonra, etkileri sistemde kalıcı olarak yerinde yapılır. Değişiklikler, sistem hatası durumunda bile devam ediyor.

### <a name="isolation-levels"></a>Yalıtım düzeyleri
Yalıtım düzeyi, işlemin diğer işlemler tarafından yapılan değişikliklerden yalıtılması gereken dereceyi tanımlar.
Güvenilir koleksiyonlarda desteklenen iki yalıtım düzeyi vardır:

* **Yinelenebilir okuma**: Bu deyimlerin değiştirilmiş ancak başka işlemler tarafından henüz onaylanmamış verileri okuyamadığından ve geçerli işlem tamamlanana kadar geçerli işlem tarafından okunan verileri değiştiremediğini belirtir. Daha ayrıntılı bilgi için bkz [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Anlık görüntü**: Bir işlemdeki herhangi bir ifadeye göre okunan verilerin, işlemin başlangıcında varolan verilerin işlemsel olarak tutarlı bir sürümü olduğunu belirtir.
  İşlem yalnızca işlemin başlangıcından önce kaydedilmiş olan veri değişikliklerini tanıyabilir.
  Geçerli işlemin başlangıcından sonra gerçekleştirilen diğer işlemler tarafından yapılan veri değişiklikleri, geçerli işlemde yürütülen deyimlere görünür değildir.
  Efekt, bir işlemdeki deyimler işlemin başlangıcında olduğu gibi yürütülen verilerin anlık görüntüsünü alır.
  Anlık görüntüler, güvenilir koleksiyonlar arasında tutarlıdır.
  Daha ayrıntılı bilgi için bkz [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Güvenilir koleksiyonlar, işleme ve işlemin oluşturulduğu sırada çoğaltmanın rolüne bağlı olarak belirli bir okuma işlemi için kullanılacak yalıtım düzeyini otomatik olarak seçer.
Aşağıda, güvenilir sözlük ve kuyruk işlemleri için yalıtım düzeyi varsayılan değerleri gösteren tablo verilmiştir.

| İşlem \ rol | Birincil | İkincil |
| --- |:--- |:--- |
| Tek bir varlık okuma |Yinelenebilir okuma |Anlık Görüntü |
| Sabit listesi, sayı |Anlık Görüntü |Anlık Görüntü |

> [!NOTE]
> Tek bir varlık Işlemleri için ortak örnekler `IReliableDictionary.TryGetValueAsync`şunlardır `IReliableQueue.TryPeekAsync`.
> 

Hem güvenilir sözlük hem de güvenilir sıra, yazma bilgilerinizi okumayı destekler.
Diğer bir deyişle, bir işlem içindeki herhangi bir yazma işlemi, aynı işleme ait olan aşağıdaki okuma için görünür olacaktır.

## <a name="locks"></a>Kilitler
Güvenilir koleksiyonlar bölümünde tüm işlemler, her türlü katı iki aşamalı kilitleme uygular: bir işlem, işlem iptal veya işleme ile sonlanana kadar elde ettiği kilitleri serbest bırakmaz.

Güvenilir sözlük tüm tek varlık işlemleri için satır düzeyinde kilitleme kullanır.
Güvenilir sıra, katı işlem FıFO özelliği için eşzamanlılık kapatır.
Güvenilir sıra, `TryPeekAsync` `EnqueueAsync` aynı anda ve/veya `TryDequeueAsync` ile bir işleme izin veren işlem düzeyinde kilitleri kullanır.
FIFO 'un, güvenilir sıranın boş olduğunu bir `TryPeekAsync` veya `TryDequeueAsync` daha fazla hizmet veriyorsa, bunların de kilitlendiğine `EnqueueAsync`göz önünde durun.

Yazma işlemleri her zaman özel kilitler alır.
Okuma işlemleri için kilitleme, birkaç faktöre bağlıdır.
Anlık görüntü yalıtımı kullanılarak gerçekleştirilen tüm okuma işlemleri boş bir kilit değildir.
Varsayılan olarak herhangi bir yinelenebilir okuma işlemi, paylaşılan kilitleri alır.
Ancak, yinelenebilir okumayı destekleyen tüm okuma işlemleri için, Kullanıcı paylaşılan kilit yerine bir güncelleştirme kilidi isteyebilir.
Güncelleştirme kilidi, birden çok işlem, olası güncelleştirmeler için kaynakları daha sonra kilitleyen yaygın bir kilitlenme biçimini engellemek için kullanılan asimetrik bir kilitdir.

Kilit uyumluluğu matrisi aşağıdaki tabloda bulunabilir:

| İstek \ verildi | None | Paylaşılan | Güncelleştirme | Özel |
| --- |:--- |:--- |:--- |:--- |
| Paylaşılan |Çakışma yok |Çakışma yok |Çakışma |Çakışma |
| Güncelleştirme |Çakışma yok |Çakışma yok |Çakışma |Çakışma |
| Özel |Çakışma yok |Çakışma |Çakışma |Çakışma |

Güvenilir koleksiyonlar API 'Lerinde zaman aşımı bağımsız değişkeni, kilitlenme algılama için kullanılır.
Örneğin, iki işlem (T1 ve T2) K1 okumayı ve güncelleştirmeyi deniyor.
Her ikisi de paylaşılan kilide bulunduğundan kilitlenmeleri mümkündür.
Bu durumda, işlemlerden biri veya her ikisi zaman aşımına uğrar.

Bu kilitlenme senaryosu, güncelleştirme kilidinin kilitlenmeleri nasıl engelleyebileceğini gösteren harika bir örnektir.

## <a name="next-steps"></a>Sonraki adımlar
* [Güvenilir Koleksiyonlar ile çalışma](service-fabric-work-with-reliable-collections.md)
* [Reliable Services bildirimleri](service-fabric-reliable-services-notifications.md)
* [Reliable Services yedekleme ve geri yükleme (olağanüstü durum kurtarma)](service-fabric-reliable-services-backup-restore.md)
* [Güvenilir durum Yöneticisi yapılandırması](service-fabric-reliable-services-configuration.md)
* [Güvenilir koleksiyonlar için geliştirici başvurusu](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)


---
title: Azure Cosmos DB yaşam süresi dolan verilerin süresi doluyor
description: TTL ile, Microsoft Azure Cosmos DB bir süre sonra otomatik olarak sistemden temizlenebilme olanağı sağlar.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: c504e2f574970142942945de5a0a9fb409bb166b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498309"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB yaşam süresi (TTL) 

**Yaşam süresi** veya TTL ile, Azure Cosmos DB belirli bir süre sonra bir kapsayıcıdan otomatik olarak öğe silme olanağı sağlar. Varsayılan olarak, kapsayıcı düzeyinde yaşam süresi ayarlayabilir ve değeri öğe temelinde geçersiz kılabilirsiniz. TTL 'yi bir kapsayıcıda veya bir öğe düzeyinde ayarladıktan sonra, Azure Cosmos DB son değiştirildikleri zamandan beri bu öğeleri otomatik olarak zaman dilimi sonra kaldırır. Yaşam süresi değeri Saniyeler içinde yapılandırılır. TTL 'yi yapılandırdığınızda, sistem, istemci uygulaması tarafından açıkça verilen silme işlemine gerek kalmadan, TTL değerine göre süresi geçen öğeleri otomatik olarak siler.

Süre sonu öğelerin silinmesi, Kullanıcı istekleri tarafından tüketilen Istek birimleri olan, sol taraftaki [Istek birimleri](request-units.md)tüketen bir arka plan görevdir. TTL 'nin süresi dolduktan sonra bile, kapsayıcı isteklerle aşırı yüklenmişse ve yeterli sayıda RU yoksa, veri silme geciktirilir. Silme işlemini gerçekleştirmek için yeterli ru varsa veriler silinir. Veri silme gecikiyor olsa da, TTL süresi dolduktan sonra veriler hiçbir sorgu (herhangi bir API tarafından) tarafından döndürülmez.

## <a name="time-to-live-for-containers-and-items"></a>Kapsayıcılar ve öğeler için yaşam süresi

Yaşam süresi değeri saniye cinsinden ayarlanır ve bir öğenin son değiştirilme zamanından Delta olarak yorumlanır. Bir kapsayıcıda veya kapsayıcı içindeki bir öğede yaşam süresi ayarlayabilirsiniz:

1. **Kapsayıcıda yaşam süresi** (`DefaultTimeToLive`kullanarak ayarla):

   - Eksik (veya null olarak ayarlandıysa), öğelerin otomatik olarak zaman aşımına ermez.

   - Varsa ve değer "-1" olarak ayarlanırsa, sonsuzluk ile eşittir ve varsayılan olarak öğelerin kullanım süreleri dolmaz.

   - Varsa ve değer bir sayı *"n"* olarak ayarlanırsa, son değiştirilme zamanından sonra öğelerin süresi *"n"* saniye dolacak.

2. **Bir öğe üzerinde yaşam süresi** (`ttl`kullanarak ayarlayın):

   - Bu özellik yalnızca `DefaultTimeToLive` varsa ve üst kapsayıcı için null olarak ayarlanmamışsa geçerlidir.

   - Varsa, üst kapsayıcının `DefaultTimeToLive` değerini geçersiz kılar.

## <a name="time-to-live-configurations"></a>Canlı yapılandırmaların süresi

* TTL bir kapsayıcıda *"n"* olarak ayarlanırsa, o kapsayıcıdaki öğelerin süresi *n* saniye sonra dolacak.  Aynı kapsayıcıda kendi yaşam süresi olan öğeler varsa,-1 ' e (süresinin dolmadığını gösterir) veya bazı öğelerin farklı bir sayı ile yaşam süresi ayarı geçersiz kılınmışsa, bu öğelerin kendisi kendi yapılandırılmış TTL değerlerine göre sona erer. 

* TTL bir kapsayıcıda ayarlanmamışsa, bu kapsayıcıdaki bir öğe üzerinde yaşam süresi etkisizdir. 

* Bir kapsayıcıda TTL değeri-1 olarak ayarlandıysa, bu kapsayıcıda yaşam süresi n olarak ayarlanan bir öğe n saniye sonra sona erer ve kalan öğelerin süresi dolmaz. 

TTL tabanlı öğeleri silme ücretsizdir. TTL süre sonu sonucu olarak öğe silindiğinde ek bir ücret (yani başka bir ru tüketilmemiş) yoktur.

## <a name="examples"></a>Örnekler

Bu bölümde, kapsayıcıya ve öğelerine atanan canlı değerler için farklı zamana sahip bazı örnekler gösterilmektedir:

### <a name="example-1"></a>Örnek 1

Kapsayıcıda TTL null olarak ayarlandı (DefaultTimeToLive = null)

|Öğe üzerinde TTL| Sonuç|
|---|---|
|TTL = null|    TTL devre dışı bırakıldı. Öğe hiçbir zaman sona ermez (varsayılan).|
|TTL =-1   |TTL devre dışı bırakıldı. Öğe hiçbir zaman sona ermez.|
|TTL = 2000 |TTL devre dışı bırakıldı. Öğe hiçbir zaman sona ermez.|


### <a name="example-2"></a>Örnek 2

Kapsayıcıda TTL-1 olarak ayarlanır (DefaultTimeToLive =-1)

|Öğe üzerinde TTL| Sonuç|
|---|---|
|TTL = null |TTL etkin. Öğe hiçbir zaman sona ermez (varsayılan).|
|TTL =-1   |TTL etkin. Öğe hiçbir zaman sona ermez.|
|TTL = 2000 |TTL etkin. Öğe 2000 saniye sonra dolacak.|


### <a name="example-3"></a>Örnek 3

Kapsayıcıda TTL 1000 olarak ayarlanır (DefaultTimeToLive = 1000)

|Öğe üzerinde TTL| Sonuç|
|---|---|
|TTL = null|    TTL etkin. Öğenin süresi 1000 saniye (varsayılan) olur.|
|TTL =-1   |TTL etkin. Öğe hiçbir zaman sona ermez.|
|TTL = 2000 |TTL etkin. Öğe 2000 saniye sonra dolacak.|

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde yaşam süresi yapılandırma hakkında bilgi edinin:

* [Yaşam süresi nasıl yapılandırılır?](how-to-time-to-live.md)

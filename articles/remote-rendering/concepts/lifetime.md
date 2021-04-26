---
title: Nesne ve kaynak ömrü
description: Farklı türler için ömür yönetimini açıklar
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593900"
---
# <a name="object-and-resource-lifetime"></a>Nesne ve kaynak ömrü

Azure uzaktan Işleme iki tür arasında ayrım yapar: **nesneler** ve **kaynaklar**.

## <a name="object-lifetime"></a>Nesne ömrü

*Nesneler* , kullanıcının kendi takdirine göre oluşturabileceğiniz, değiştirebileceği ve yok edileceği şeyler olarak değerlendirilir. Nesneler serbestçe yinelenebilir ve her örnek zaman içinde zaman içinde olabilir. Sonuç olarak, [varlıklar](entities.md) ve [Bileşenler](components.md) nesnelerdir.

Nesnelerin ömrü, Kullanıcı denetimi altında tamamen yapılır. Ancak, istemci tarafı gösteriminin yaşam süresi ile ilgili değildir. Ve gibi sınıflar, `Entity` `Component` `Destroy` Uzak işleme konağındaki nesneyi serbest bırakmak için çağrılması gereken bir işleve sahiptir. Ayrıca, `Entity.Destroy()` varlığı, alt öğelerini ve söz konusu hiyerarşideki tüm bileşenleri yok eder.

## <a name="resource-lifetime"></a>Kaynak ömrü

*Kaynaklar* , ömrü tamamen uzaktan işleme ana bilgisayarı tarafından yönetilmekte olan şeylerdir. Kaynaklar dahili olarak sayılır. Hiç kimse hiç başvurmuyorsa serbest bırakılır.

Kaynakların çoğu, genellikle bir dosyadan yükleyerek dolaylı olarak oluşturulabilir. Aynı dosya birden çok kez yüklendiğinde, Azure uzaktan Işleme aynı başvuruyu döndürür ve verileri tekrar yüklemez.

Örneğin, [kafesler](meshes.md) ve [dokular](textures.md)için çok sayıda kaynak sabittir. Bazı kaynaklar değişebilir, ancak örneğin [malzemeleridir](materials.md). Kaynaklar genellikle paylaşıldığından, bir kaynağı değiştirmek birden çok nesneyi etkileyebilir. Örneğin, bir malzemenin rengini değiştirmek, kafeslerin kullanıldığı tüm nesnelerin rengini değiştirecek ve bu da bu malzemeye başvuracaktır.

### <a name="built-in-resources"></a>Yerleşik kaynaklar

Azure uzaktan Işleme, çağrısı sırasında ilgili tanımlayıcılarını önceden bekleyen bir şekilde yüklenebilecek bazı yerleşik kaynaklar içerir `builtin://` `RenderingSession.Connection.LoadXYZAsync()` . Kullanılabilir yerleşik kaynaklar ilgili her özellik için belgelerde listelenmiştir. Örneğin, [Sky bölümü](../overview/features/sky.md) yerleşik gök dokularını listeler.

## <a name="general-lifetime"></a>Genel ömür

Tüm nesnelerin ve kaynakların yaşam süresi bağlantıya bağlanır. Her şeyin bağlantısını kesme sırasında atılır. Aynı oturuma yeniden bağlanırken, sahne grafiği boş olur ve tüm kaynaklar temizlenir.

Uygulamada, bir bağlantı kesildikten sonra bir oturum için aynı kaynağı yüklemek genellikle ilk kez daha hızlıdır. Bu durum, çoğu kaynağın Azure depolama 'dan ilk kez indirilmesi gerektiğinden, önemli ölçüde zaman tasarrufu gerektirdiğinden oluşan durumdur.

## <a name="next-steps"></a>Sonraki adımlar

* [Varlıklar](entities.md)
* [Bileşenler](components.md)
* [Modeller](models.md)

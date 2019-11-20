---
title: Değişiklik akışı işlemcisi başlangıç zamanı-Azure Cosmos DB nasıl yapılandırılır
description: Değişiklik akışı işlemcisinin belirli bir tarih ve saatten okumaya başlaması için nasıl yapılandırılacağını öğrenin
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093639"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Değişiklik akışı işlemcisi başlangıç saatini yapılandırma

Bu makalede, [değişiklik akışı işlemcisini](./change-feed-processor.md) belirli bir tarih ve saatten okumayı başlatacak şekilde nasıl yapılandırabileceğiniz açıklanmaktadır.

## <a name="default-behavior"></a>Varsayılan davranış

Bir değişiklik akışı işlemcisi ilk kez başladığında, kira kapsayıcısını başlatır ve [işlem yaşam döngüsünü](./change-feed-processor.md#processing-life-cycle)başlatır. Değişiklik akışı işlemcisi ilk kez başlatılmadan önce kapsayıcıda gerçekleşen tüm değişiklikler algılanmaz.

## <a name="reading-from-a-previous-date-and-time"></a>Önceki bir tarih ve saatten okuma

Değişiklik akışı işlemcisini, **belirli bir tarih ve saatte**başlayan değişiklikleri okumak için, bir a `DateTime` `WithStartTime` örneğini Oluşturucu uzantısına geçirerek başlatmak mümkündür:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Değişiklik akışı işlemcisi, belirli bir tarih ve saat için başlatılır ve sonrasında gerçekleşen değişiklikleri okumaya başlar.

## <a name="reading-from-the-beginning"></a>Baştan itibaren okunuyor

Veri geçişleri veya bir kapsayıcının tüm geçmişinin çözümlenmesi gibi diğer senaryolarda, **Bu kapsayıcının yaşam süresinden itibaren**değişiklik akışını okuduk. Bunu yapmak için, Oluşturucu uzantısı üzerinde `WithStartTime` kullanabiliriz, `DateTime.MinValue.ToUniversalTime()`ancak bu, en düşük `DateTime` değerin UTC gösterimini oluşturan, şöyle olabilir:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Değişiklik akışı işlemcisi başlatılır ve kapsayıcının yaşam süresinden itibaren yapılan değişiklikleri okumaya başlar.

> [!NOTE]
> Bu özelleştirme seçenekleri yalnızca değişiklik akışı işlemcisinin başlangıç noktasını ayarlamaya çalışır. Kiralamalar kapsayıcısı ilk kez başlatıldıktan sonra, bunların değiştirilmesinin etkisi olmaz.

> [!NOTE]
> Bir zaman noktası belirtirken, yalnızca kapsayıcıda bulunan öğeler için değişiklikler okunacaktır. Bir öğe silinirse, değişiklik akışındaki geçmişi de kaldırılır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 'da kullanım örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 'da ek örnekler](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Sonraki adımlar

Şimdi aşağıdaki makalelerde akış işlemcisini Değiştir hakkında daha fazla bilgi edinebilirsiniz:

* [Değişiklik akışı işlemcisine genel bakış](change-feed-processor.md)
* [Değişiklik akışı tahmin aracı 'ı kullanma](how-to-use-change-feed-estimator.md)

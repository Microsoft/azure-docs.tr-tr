---
title: Değişiklik akışı işlemcisi kitaplığından .NET v3 SDK Azure Cosmos DB geçirin
description: Değişiklik akışı işlemcisi kitaplığını kullanarak uygulamanızı Azure Cosmos DB SDK V3 'e geçirmeyi öğrenin
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ce2d4d3ad3ae349718f01584ec077b18e11e4f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341272"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Değişiklik akışı işlemcisi kitaplığından .NET v3 SDK Azure Cosmos DB geçirin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, [değişiklik akışı işlemci kitaplığını](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) kullanan mevcut bir uygulamanın kodunu, .NET SDK 'sının en son sürümündeki (.net v3 SDK olarak da bilinir), [akışı Değiştir](change-feed.md) özelliğine geçirmek için gereken adımlar açıklanmaktadır.

## <a name="required-code-changes"></a>Gerekli kod değişiklikleri

.NET v3 SDK 'sının birkaç önemli değişikliği vardır ve uygulamanızı geçirmeye yönelik temel adımlar aşağıda verilmiştir:

1. `DocumentCollectionInfo` `Container` İzlenen ve kira kapsayıcıları için örnekleri başvurulara dönüştürün.
1. Kullanan özelleştirmeler `WithProcessorOptions` `WithLeaseConfiguration` `WithPollInterval` , Aralık için ve aralıklar için, `WithStartTime` [Başlangıç saati için](./change-feed-processor.md#starting-time)ve `WithMaxItems` en fazla öğe sayısını tanımlamak için güncelleştirilmeleri gerekir.
1. `processorName`Üzerinde `GetChangeFeedProcessorBuilder` yapılandırılan değerle eşleşecek şekilde ' `ChangeFeedProcessorOptions.LeasePrefix` i ayarlayın veya başka bir şekilde kullanın `string.Empty` .
1. Değişiklikler artık bir olarak teslim değildir, `IReadOnlyList<Document>` bunun yerine `IReadOnlyCollection<T>` tanımlamanız gereken bir tür, artık `T` temel öğe sınıfı yoktur.
1. Değişiklikleri işlemek için artık bir uygulamaya ihtiyacınız yoktur, bunun yerine [bir temsilci tanımlamanız](change-feed-processor.md#implementing-the-change-feed-processor)gerekir. Temsilci bir statik Işlev olabilir veya yürütmeler genelinde durum korumanız gerekiyorsa, kendi sınıfınızı oluşturabilir ve temsilci olarak bir örnek yöntemi geçirebilirsiniz.

Örneğin, değişiklik akışı işlemcisini derlemek için özgün kod aşağıdaki gibi görünür:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Geçirilen kod şöyle görünür:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

Ve temsilci bir statik yöntem olabilir:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Eyalet ve Kiralama kapsayıcısı

Değişiklik akışı işlemci kitaplığına benzer şekilde, .NET v3 SDK 'daki değişiklik akışı özelliği, durumu depolamak için bir [Kiralama kapsayıcısı](change-feed-processor.md#components-of-the-change-feed-processor) kullanır. Ancak, şemalar farklıdır.

SDK V3 değişiklik akışı işlemcisi, eski kitaplık durumunu algılayacak ve geçirilen uygulama kodunun ilk yürütmesi üzerine otomatik olarak yeni şemaya geçirecektir. 

Eski kodu kullanarak uygulamayı güvenle durdurabilir, kodu yeni sürüme geçirebilir, geçirilen uygulamayı başlatabilir ve uygulama durdurulduğunda gerçekleşen tüm değişiklikler yeni sürüm tarafından oluşturulur ve işlenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 'da kullanım örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 'da ek örnekler](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Sonraki adımlar

Şimdi aşağıdaki makalelerde akış işlemcisini Değiştir hakkında daha fazla bilgi edinebilirsiniz:

* [Değişiklik akışı işlemcisine genel bakış](change-feed-processor.md)
* [Değişiklik akışı tahmin aracını kullanma](how-to-use-change-feed-estimator.md)
* [Değişiklik akışı işlemcisi başlangıç zamanı](./change-feed-processor.md#starting-time)
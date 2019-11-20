---
title: Azure Cosmos DB değişiklik akışı işlemcisi kitaplığıyla çalışma
description: Azure Cosmos DB değişiklik akışı işlemci kitaplığını kullanma.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 4bd7a31abf47664d1a6ffdd39fe46d9370dbbc97
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757039"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB akış işlemcisini değiştirme 

Değişiklik akışı işlemcisi, [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)'nin bir parçasıdır. Değişiklik akışını okuma ve olay işlemeyi birden çok tüketiciye etkin bir şekilde dağıtma sürecini basitleştirir.

Değişiklik akışı işlemci kitaplığının ana avantajı, değişiklik akışındaki tüm olayların "en az bir kez" teslimini sağlayan hataya dayanıklı davranıştır.

## <a name="components-of-the-change-feed-processor"></a>Değişiklik akışı işlemcisinin bileşenleri

Değişiklik akışı işlemcisini uygulayan dört ana bileşen vardır: 

1. **İzlenen kapsayıcı:** İzlenen kapsayıcı, değişiklik beslemenin oluşturulduğu verileri içerir. İzlenen kapsayıcıya yapılan tüm ekler ve güncelleştirmeler kapsayıcının değişiklik akışına yansıtılır.

1. **Kira kapsayıcısı:** Kira kapsayıcısı, bir durum depolaması görevi görür ve değişiklik akışını birden fazla çalışan genelinde işlemeyi düzenler. Kira kapsayıcısı, izlenen kapsayıcı veya ayrı bir hesapta aynı hesapta depolanabilir. 

1. **Ana bilgisayar:** Ana bilgisayar, değişiklikleri dinlemek için akış işlemcisini Değiştir ' i kullanan bir uygulama örneğidir. Aynı kira yapılandırmasına sahip birden çok örnek paralel olarak çalıştırılabilir, ancak her örnek farklı bir **örnek adına**sahip olmalıdır. 

1. **Temsilci:** Temsilci, geliştirici tarafından değişiklik akışı işlemcisinin okuduğu her değişiklik kümesi için ne yapmak istediğinizi tanımlayan koddur. 

Bu dört öğelerin değişiklik akışı işlemcisi ile birlikte nasıl çalıştığını anlamak için aşağıdaki diyagramda bir örneğe bakalım. İzlenen kapsayıcı belgeleri depolar ve bölüm anahtarı olarak ' City ' kullanır. Bölüm anahtarı değerlerinin öğeler içeren aralıklarda dağıtıldığını görüyoruz. İki ana bilgisayar örneği bulunur ve değişiklik akışı işlemcisi, işlem dağıtımını en üst düzeye çıkarmak için her örneğe farklı bölüm anahtarı değerlerini atanıyor. Her Aralık paralel olarak okunmakta ve ilerleme durumu kira kapsayıcısındaki diğer aralıklardan ayrı olarak korunur.

![Akış işlemcisi örneğini değiştirme](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Değişiklik akışı işlemcisini uygulama

Giriş noktası her zaman izlenen kapsayıcı olur, bu `Container` örneğinden `GetChangeFeedProcessorBuilder` çağırabilirsiniz:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

İlk parametre, bu işlemcinin hedefini açıklayan ayrı bir addır ve ikinci ad, değişiklikleri işleyecek temsilci uygulamasıdır. 

Bir temsilci örneği şöyle olabilir:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Son olarak, `WithInstanceName` ile bu işlemci örneği için bir ad tanımlarsınız ve bu kapsayıcı, `WithLeaseContainer` kira durumunun bakımını sağlar.

@No__t_0 çağırmak, size `StartAsync` çağırarak başlayabilmeniz için kullanabileceğiniz işlemci örneğini verecektir.

## <a name="processing-life-cycle"></a>İşlem yaşam döngüsü

Bir konak örneğinin normal yaşam döngüsü şu şekilde olur:

1. Değişiklik akışını okuyun.
1. Değişiklik yoksa, önceden tanımlanmış bir süre (Oluşturucu `WithPollInterval` özelleştirilebilir) için uyku moduna geçin ve #1 gidin.
1. Değişiklikler varsa **temsilciyi temsilciye**gönderin.
1. Temsilci değişiklikleri **başarıyla**işlemeyi tamamladığında, kira deposunu en son işlenen zaman noktasıyla güncelleştirin ve #1 gidin.

## <a name="error-handling"></a>Hata işleme

Değişiklik akışı işlemcisi, Kullanıcı kodu hatalarına karşı dayanıklı olur. Bu, temsilci uygulamanızın işlenmeyen bir özel durum (adım #4) varsa, belirli bir değişiklik kümesini işleyen iş parçacığının durdurulması ve yeni bir iş parçacığının oluşturulması anlamına gelir. Yeni iş parçacığı, kira deposunun Bu bölüm anahtarı değerleri aralığına sahip olduğu en son noktayı denetler ve bu durumda, temsilci üzerinde aynı toplu değişiklik kümesini etkin bir şekilde göndererek buradan yeniden başlatılır. Bu davranış, temsilci değişiklikleri doğru bir şekilde işleyene kadar devam eder ve değişiklik akışı işlemcisinin "en az bir kez" garantisi vardır çünkü temsilci kodu oluşturursa bu toplu işi yeniden dener.

## <a name="dynamic-scaling"></a>Dinamik ölçeklendirme

Giriş sırasında belirtildiği gibi, değişiklik akışı işlemcisi, işlem akışını otomatik olarak birden çok örneğe dağıtabilir. Değişiklik akışı işlemcisini kullanarak uygulamanızın birden çok örneğini dağıtabilir ve bundan faydalanabilirsiniz, tek önemli gereksinimler şunlardır:

1. Tüm örneklerin aynı kira kapsayıcı yapılandırmasına sahip olması gerekir.
1. Tüm örneklerin aynı iş akışı adına sahip olması gerekir.
1. Her örneğin farklı bir örnek adı (`WithInstanceName`) olması gerekir.

Bu üç koşul geçerliyse, değişiklik akışı işlemcisi, eşit bir dağıtım algoritması kullanarak, tüm çalışan örnekler ve paralel hale getirmek işlem genelindeki kira kapsayıcısındaki tüm kiraları dağıtır. Tek bir kiralamanın belirli bir zamanda yalnızca bir örneğe ait olması, en fazla örnek sayısının kira sayısına eşit olması için.

Örnek sayısı büyüyebilir ve küçülebilir ve değişiklik akışı işlemcisi, uygun şekilde yeniden dağıtarak yükü dinamik olarak ayarlar.

Üstelik, değişiklik akışı işlemcisi, verimlilik veya depolama arttıkça dinamik olarak kapsayıcılar ölçeğinde değişiklik yapabilir. Kapsayıcınız büyüdükçe, değişiklik akışı işlemcisi, kiralamaları dinamik olarak artırarak ve yeni kiraları mevcut örnekler arasında dağıtarak bu senaryoları saydam olarak gerçekleştirir.

## <a name="change-feed-and-provisioned-throughput"></a>Akışı ve sağlanan aktarım hızını değiştirme

Cosmos kapsayıcılarının içindeki ve içindeki veri hareketleri her zaman RUs kullandığından, kullanılan ru için ücretlendirilirsiniz. Kira kapsayıcısı tarafından tüketilen RUs için ücretlendirilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 'da kullanım örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 'da ek örnekler](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Sonraki adımlar

Şimdi aşağıdaki makalelerde akış işlemcisini Değiştir hakkında daha fazla bilgi edinebilirsiniz:

* [Değişiklik akışına genel bakış](change-feed.md)
* [Değişiklik akışı işlemci kitaplığından geçiş yapma](how-to-migrate-from-change-feed-library.md)
* [Değişiklik akışı tahmin aracı 'ı kullanma](how-to-use-change-feed-estimator.md)
* [Akış işlemcisi başlangıç saatini değiştir](how-to-configure-change-feed-start-time.md)

---
title: Cosmos DB için Azure Işlevleri tetikleyicisi 'ni kullanırken sorunları tanılayın ve sorun giderin
description: Cosmos DB için Azure Işlevleri tetikleyicisi kullanılırken yaygın sorunlar, geçici çözümler ve Tanılama adımları
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e3ff86770ec0337c9a4a11b30c6d88e8365bfa24
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064099"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetikleyicisi 'ni kullanırken sorunları tanılayın ve sorun giderin

Bu makalede, [Cosmos DB Için Azure işlevleri tetikleyicisi](change-feed-functions.md)kullandığınızda yaygın sorunlar, geçici çözümler ve Tanılama adımları ele alınmaktadır.

## <a name="dependencies"></a>Bağımlılıklar

Cosmos DB için Azure Işlevleri tetiklemesi ve bağlamaları, temel Azure Işlevleri çalışma zamanı üzerinden uzantı paketlerine bağlıdır. Düzeltmeler ve karşılaşabileceğiniz olası sorunları gidermeye yönelik yeni özellikler dahil olabileceğinden, her zaman bu paketleri güncel tutun:

* Azure Işlevleri v2 için bkz. [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Azure Işlevleri v1 için bkz. [Microsoft. Azure. WebJobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Bu makale, açıkça belirtilmediği takdirde, her zaman çalışma zamanına her bahsedildiğinde Azure Işlevleri v2 'ye başvuracaktır.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Azure Cosmos DB SDK 'Yı bağımsız olarak kullanın

Uzantı paketinin temel işlevselliği, Cosmos DB için Azure Işlevleri tetikleyicisi ve bağlamaları için destek sağlamaktır. Ayrıca, tetikleyici ve bağlamaları kullanmadan Azure Cosmos DB programlı bir şekilde etkileşim kurmak istiyorsanız yararlı olacak [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)' yı da içerir.

Azure Cosmos DB SDK 'yı kullanmak istiyorsanız, projenize başka bir NuGet paket başvurusu eklemediğinizden emin olun. Bunun yerine, **SDK başvurusunun Azure işlevleri ' uzantı paketi aracılığıyla çözümlenmesine izin verin**. Azure Cosmos DB SDK 'sını tetikleyiciden ve bağlamalardan ayrı kullanın

Ayrıca, [Azure Cosmos DB SDK istemcisinin](./sql-api-sdk-dotnet-core.md)kendi örneğinizi el ile oluşturuyorsanız, tek [bir desenli yaklaşım kullanarak](../azure-functions/manage-connections.md#documentclient-code-example-c)istemcinin yalnızca bir örneğine sahip olma örüntüsünün izlenmesi gerekir. Bu işlem, işlemlerinizin olası yuva sorunlarından kaçınacaktır.

## <a name="common-scenarios-and-workarounds"></a>Yaygın senaryolar ve geçici çözümler

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Işlevi hata iletisi koleksiyonu yok hatasıyla başarısız oluyor

Azure Işlevi şu hata iletisiyle başarısız oldu "' koleksiyon-adı ' (veritabanındaki ' veritabanı-adı ') veya ' Collection2-Name ' kira koleksiyonu (' Veritabanı2-Name ' veritabanında) Her iki koleksiyon da dinleyici başlamadan önce mevcut olmalıdır. Kira koleksiyonunu otomatik olarak oluşturmak için ' CreateLeaseCollectionIfNotExists ' değerini ' true ' olarak ayarlayın.

Bu, tetikleyicinin çalışması için gereken Azure Cosmos kapsayıcılarının bir veya her ikisinin mevcut olmadığı ya da Azure Işlevi için ulaşılamaz olmadığı anlamına gelir. Hatanın kendisi, yapılandırmanıza bağlı olarak **hangi Azure Cosmos veritabanı ve kapsayıcılarının bakıyor olduğunu bildirir** .

1. `ConnectionStringSetting` özniteliğini ve **Azure işlev uygulaması mevcut bir ayara başvurmuş**olduğunu doğrulayın. Bu öznitelikteki değer bağlantı dizesinin kendisi olmaması gerekir, ancak yapılandırma ayarının adı.
2. `databaseName` ve `collectionName` Azure Cosmos hesabınızda mevcut olduğunu doğrulayın. Otomatik değer değişimi kullanıyorsanız (`%settingName%` desenleri kullanarak), ayarın adının Azure İşlev Uygulaması bulunduğundan emin olun.
3. `LeaseCollectionName/leaseCollectionName`belirtmezseniz, varsayılan değer "kiralamalar" dır. Bu kapsayıcının var olduğunu doğrulayın. İsteğe bağlı olarak, Tetikleyicinizdeki `CreateLeaseCollectionIfNotExists` özniteliğini otomatik olarak oluşturmak için `true` olarak ayarlayabilirsiniz.
4. Azure Işlevini engellemediğini öğrenmek için [Azure Cosmos hesabınızın güvenlik duvarı yapılandırmasını](how-to-configure-firewall.md) doğrulayın.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Işlevi "paylaşılan verimlilik koleksiyonu bir bölüm anahtarına sahip olmalıdır" ile başlayamaz

Azure Cosmos DB uzantısının önceki sürümleri, [paylaşılan bir üretilen iş veritabanı](./set-throughput.md#set-throughput-on-a-database)içinde oluşturulan bir kira kapsayıcısını kullanmayı desteklemiyor. Bu sorunu çözmek için [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) uzantısını en son sürümü almak için güncelleştirin.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Işlevi, "bölümlenmiş ise, Bölüm anahtarının kimliğe eşit olması gerekir." kira koleksiyonu ile başlayamaz.

Bu hata, geçerli kiralamalar kapsayıcının bölümlenmesi anlamına gelir, ancak bölüm anahtarı yolu `/id`değildir. Bu sorunu çözmek için, kiralamalar kapsayıcısını bölüm anahtarı olarak `/id` yeniden oluşturmanız gerekir.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"Değer null olamaz. Tetikleyici çalıştırmayı denediğinizde, Azure işlevleriniz içindeki parametre adı: o "

Bu sorun, Azure portal kullanıyorsanız ve tetikleyiciyi kullanan bir Azure Işlevi incelenirken ekranda **Çalıştır** düğmesini seçmeyi denerseniz görüntülenir. Tetiklemenin başlamak için çalıştırması gerekmez, Azure Işlevi dağıtıldığında otomatik olarak başlatılır. Azure portal Azure Işlevinin günlük akışını denetlemek istiyorsanız, izlenen kapsayıcınıza gitmeniz ve yeni öğeler eklemeniz yeterlidir. tetikleyiciyi otomatik olarak görürsünüz.

### <a name="my-changes-take-too-long-to-be-received"></a>Değişikliklerimin alınması çok uzun sürecek

Bu senaryoda birden çok neden olabilir ve bunların tümü denetlenmelidir:

1. Azure İşleviniz Azure Cosmos hesabınızla aynı bölgeye mi dağıtıldı? En iyi ağ gecikme süresi için hem Azure İşlevinin hem de Azure Cosmos hesabınızın aynı Azure bölgesinde birlikte bulunması gerekir.
2. Azure Cosmos kapsayıcınızda gerçekleşen değişiklikler sürekli mi yoksa aralıklı mı?
Aralıklıysa, değişikliklerin depolanması ile Azure İşlevinin bunları alması arasında biraz gecikme olabilir. Bunun nedeni dahili olarak tetikleyici Azure Cosmos kapsayıcınızda değişiklikleri denetleyip hiç okunmayı bekleyen değişiklik olmadığını bulduğunda, yeni değişiklikler için denetleme yapmadan önce yapılandırılabilir bir süre (varsayılan olarak 5 saniye) uykuya geçer (yüksek RU tüketimini önlemek için). Bu uyku süresini tetikleyicinizin [yapılandırmasındaki](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) `FeedPollDelay/feedPollDelay` ayarıyla yapılandırabilirsiniz (değerin milisaniye cinsinden olması beklenir).
3. Azure Cosmos Kapsayıcınız [hız sınırlı](./request-units.md)olabilir.
4. Özel bir tercih edilen bağlantı sırası tanımlamak üzere Azure bölgelerinin virgülle ayrılmış bir listesini belirtmek için tetikleyicinizdeki `PreferredLocations` özniteliğini kullanabilirsiniz.

### <a name="some-changes-are-missing-in-my-trigger"></a>Tetikleyicimde bazı değişiklikler yok

Azure Cosmos kapsayıcıda gerçekleşen bazı değişikliklerin Azure Işlevi tarafından çekilmediğini fark ederseniz, gerçekleşmesi gereken bir ilk araştırma adımı vardır.

Azure Işleviniz değişiklikleri aldığında, genellikle bunları işler ve isteğe bağlı olarak başka bir hedefe gönderebilirsiniz. Eksik değişiklikleri araştırırken, hedef üzerinde değil, alma noktasında (Azure Işlevi başladığında) **hangi değişikliklerin alındığını ölçdiğinizden** emin olun.

Hedefte bazı değişiklikler eksikse, bu, değişiklikler alındıktan sonra Azure Işlev yürütmesi sırasında bazı hatalar meydana geliyor olabilir.

Bu senaryoda, en iyi işlem, kodunuzda `try/catch` blokları eklemek, belirli bir öğe alt kümesi için herhangi bir hatayı tespit etmek ve bunları buna göre işlemek (daha fazla için başka bir depolamaya göndermek için). analiz veya yeniden deneme). 

> [!NOTE]
> Azure Işlevleri Cosmos DB için tetikleyerek, kod yürütmeyle ilgili işlenmeyen bir özel durum oluşursa, varsayılan olarak bir grup değişikliği yeniden denenmez. Bu, değişikliklerin hedefe ulaşamamasının nedeni, bunları İşleyemeyeceğiniz anlamına gelir.

Tetikleyicinizin tümünde bazı değişikliklerin alınmadığını fark ederseniz, en yaygın senaryo, **çalışan başka bir Azure işlevi**olduğunu fark edersiniz. Bu, Azure 'da dağıtılan başka bir Azure Işlevi veya bir geliştirici makinesinde **tam olarak aynı yapılandırmaya** (aynı izlenen ve kira kapsayıcıları) sahip olan bir Azure işlevi olabilir ve bu Azure işlevi, yaptığınız değişikliklerin bir alt kümesini çalmaya çalışıyor Azure Işlevinizin işlemesini bekler.

Ayrıca, kaç Azure İşlev Uygulaması örneğinin çalıştığını biliyorsanız senaryo doğrulanabilir. Kira kapsayıcınızı inceleyebilir ve içindeki kira öğelerinin sayısını saydıysanız, içindeki `Owner` özelliğinin farklı değerleri, İşlev Uygulaması örneklerinin sayısına eşit olmalıdır. Bilinen Azure İşlev Uygulaması örneklerinden daha fazla sayıda Sahip varsa, bu fazladan sahiplerin değişiklikleri "çaldığı" anlaşılır.

Bu durumun kolay bir yolu, yeni/farklı bir değere sahip işlevinizde `LeaseCollectionPrefix/leaseCollectionPrefix` uygulamak ya da başka bir kira kapsayıcuyla test etmek.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>Kapsayıcımda bulunan tüm öğelerin başlangıçtan itibaren yeniden başlatılması ve yeniden işlenmesi gerekiyor 
Bir kapsayıcıdaki tüm öğeleri baştan sonra yeniden işlemek için:
1. Şu anda çalışıyorsa Azure işlevinizi durdurun. 
1. Kira koleksiyonundaki belgeleri silin (veya boş olması için kira koleksiyonunu silip yeniden oluşturun)
1. İşlevinizdeki [Startfromstarted](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) cosmosdbtrigger özniteliğini doğru olarak ayarlayın. 
1. Azure işlevini yeniden başlatın. Şimdi, başlangıçtan itibaren tüm değişiklikleri okur ve işler. 

[Startfromstart](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) 'ı true olarak ayarlamak, Azure işlevine geçerli saat yerine koleksiyonun geçmişinden başlayarak okuma işlemi başlatmasını bildirir. Bu yalnızca, zaten oluşturulan kiralamalar (örn. kiralamalar koleksiyonundaki belgeler) olmadığında işe yarar. Zaten oluşturulan kiralamalar varsa, bu özelliğin true olarak ayarlanması etkisizdir; Bu senaryoda, bir işlev durdurulduğunda ve yeniden başlatıldığında, kiralamalar koleksiyonunda tanımlandığı gibi son denetim noktasından okumaya başlar. Baştan sonra yeniden işlemek için yukarıdaki 1-4 adımları izleyin.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Bağlama yalnızca IReadOnlyList\<Document > veya JArray ile yapılabilir

Bu hata, Azure Işlevleri projeniz (veya başvurulan herhangi bir proje), [Azure işlevleri Cosmos DB uzantısı](./troubleshoot-changefeed-functions.md#dependencies)tarafından sağlanenden farklı bir SÜRÜMLE Azure Cosmos DB SDK 'ya el ile NuGet başvurusu içeriyorsa meydana gelir.

Bu durumun geçici çözümü için, eklenen el ile NuGet başvurusunu kaldırın ve Azure Cosmos DB SDK başvurusunun Azure Işlevleri Cosmos DB Uzantı paketi aracılığıyla çözümlendiğine izin verin.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Değişikliklerin algılanması için Azure Işlevinin yoklama aralığı değiştiriliyor

Daha önce [değişikliklerinizin alınması çok uzun sürme](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)için, Azure işlevi yeni değişiklikler denetlenmeden önce yapılandırılabilir bir süre (varsayılan olarak 5 saniye) için uykuya geçecek (yüksek ru tüketimine engel olmak için). Bu uyku süresini tetikleyicinizin [yapılandırmasındaki](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) `FeedPollDelay/feedPollDelay` ayarıyla yapılandırabilirsiniz (değerin milisaniye cinsinden olması beklenir).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Işlevleriniz için izlemeyi etkinleştirme](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK sorunlarını giderme](./troubleshoot-dot-net-sdk.md)

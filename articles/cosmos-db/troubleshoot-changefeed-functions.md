---
title: Cosmos DB için Azure Işlevleri tetikleyicisi 'ni kullanırken karşılaşılan sorunları giderme
description: Cosmos DB için Azure Işlevleri tetikleyicisi kullanılırken yaygın sorunlar, geçici çözümler ve Tanılama adımları
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825621"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetikleyicisi 'ni kullanırken sorunları tanılayın ve sorun giderin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, [Cosmos DB Için Azure işlevleri tetikleyicisi](change-feed-functions.md)kullandığınızda yaygın sorunlar, geçici çözümler ve Tanılama adımları ele alınmaktadır.

## <a name="dependencies"></a>Bağımlılıklar

Cosmos DB için Azure Işlevleri tetiklemesi ve bağlamaları, temel Azure Işlevleri çalışma zamanı üzerinden uzantı paketlerine bağlıdır. Düzeltmeler ve karşılaşabileceğiniz olası sorunları gidermeye yönelik yeni özellikler dahil olabileceğinden, her zaman bu paketleri güncel tutun:

* Azure Işlevleri v2 için bkz. [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Azure Işlevleri v1 için bkz. [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Bu makale, açıkça belirtilmediği takdirde, her zaman çalışma zamanına her bahsedildiğinde Azure Işlevleri v2 'ye başvuracaktır.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Azure Cosmos DB SDK 'Yı bağımsız olarak kullanın

Uzantı paketinin temel işlevselliği, Cosmos DB için Azure Işlevleri tetikleyicisi ve bağlamaları için destek sağlamaktır. Ayrıca, tetikleyici ve bağlamaları kullanmadan Azure Cosmos DB programlı bir şekilde etkileşim kurmak istiyorsanız yararlı olacak [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)' yı da içerir.

Azure Cosmos DB SDK 'yı kullanmak istiyorsanız, projenize başka bir NuGet paket başvurusu eklemediğinizden emin olun. Bunun yerine, **SDK başvurusunun Azure işlevleri ' uzantı paketi aracılığıyla çözümlenmesine izin verin**. Azure Cosmos DB SDK 'sını tetikleyiciden ve bağlamalardan ayrı kullanın

Ayrıca, [Azure Cosmos DB SDK istemcisinin](./sql-api-sdk-dotnet-core.md)kendi örneğinizi el ile oluşturuyorsanız, tek [bir desenli yaklaşım kullanarak](../azure-functions/manage-connections.md#documentclient-code-example-c)istemcinin yalnızca bir örneğine sahip olma örüntüsünün izlenmesi gerekir. Bu işlem, işlemlerinizin olası yuva sorunlarından kaçınacaktır.

## <a name="common-scenarios-and-workarounds"></a>Yaygın senaryolar ve geçici çözümler

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Işlevi hata iletisi koleksiyonu yok hatasıyla başarısız oluyor

Azure Işlevi şu hata iletisiyle başarısız oldu "' koleksiyon-adı ' (veritabanındaki ' veritabanı-adı ') veya ' Collection2-Name ' kira koleksiyonu (' Veritabanı2-Name ' veritabanında) Her iki koleksiyon da dinleyici başlamadan önce mevcut olmalıdır. Kira koleksiyonunu otomatik olarak oluşturmak için ' CreateLeaseCollectionIfNotExists ' değerini ' true ' olarak ayarlayın.

Bu, tetikleyicinin çalışması için gereken Azure Cosmos kapsayıcılarının bir veya her ikisinin mevcut olmadığı ya da Azure Işlevi için ulaşılamaz olmadığı anlamına gelir. Hatanın kendisi, yapılandırmanıza bağlı olarak **hangi Azure Cosmos veritabanı ve kapsayıcısının hangi tetikleyici olduğunu bildirir** .

1. `ConnectionStringSetting`Özniteliğini ve **Azure işlev uygulaması var olan bir ayara başvurmuş** olduğunu doğrulayın. Bu öznitelikteki değer bağlantı dizesinin kendisi olmaması gerekir, ancak yapılandırma ayarının adı.
2. `databaseName`Ve ' nin `collectionName` Azure Cosmos hesabınızda mevcut olduğunu doğrulayın. Otomatik değer değiştirme ( `%settingName%` desenler kullanarak) kullanıyorsanız, ayarın adının Azure işlev uygulaması bulunduğundan emin olun.
3. Bir belirtmezseniz `LeaseCollectionName/leaseCollectionName` , varsayılan değer "kiralamalar" dır. Bu kapsayıcının var olduğunu doğrulayın. İsteğe bağlı `CreateLeaseCollectionIfNotExists` olarak, tetikleyicinizdeki özniteliğini `true` otomatik olarak oluşturmak için olarak ayarlayabilirsiniz.
4. Azure Işlevini engellemediğini öğrenmek için [Azure Cosmos hesabınızın güvenlik duvarı yapılandırmasını](how-to-configure-firewall.md) doğrulayın.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Işlevi "paylaşılan verimlilik koleksiyonu bir bölüm anahtarına sahip olmalıdır" ile başlayamaz

Azure Cosmos DB uzantısının önceki sürümleri, [paylaşılan bir üretilen iş veritabanı](./set-throughput.md#set-throughput-on-a-database)içinde oluşturulan bir kira kapsayıcısını kullanmayı desteklemiyor. Bu sorunu çözmek için [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) uzantısını en son sürümü almak için güncelleştirin.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure Işlevi, "Bu işlem için PartitionKey sağlanmalıdır."

Bu hata, şu anda eski [uzantı bağımlılığıyla](#dependencies)bölümlenmiş bir kira koleksiyonu kullandığınız anlamına gelir. Kullanılabilir en son sürüme yükseltin. Şu anda Azure Işlevleri v1 üzerinde çalıştırıyorsanız, Azure Işlevleri v2 'ye yükseltmeniz gerekir.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Işlevi, "bölümlenmiş ise, Bölüm anahtarının kimliğe eşit olması gerekir." kira koleksiyonu ile başlayamaz.

Bu hata, geçerli kiralamalar kapsayıcının bölümlenmesi anlamına gelir, ancak bölüm anahtarı yolu değildir `/id` . Bu sorunu çözmek için, kira kapsayıcısını bölüm anahtarı olarak yeniden oluşturmanız gerekir `/id` .

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"Değer null olamaz. Tetikleyici çalıştırmayı denediğinizde, Azure işlevleriniz içindeki parametre adı: o "

Bu sorun, Azure portal kullanıyorsanız ve tetikleyiciyi kullanan bir Azure Işlevi incelenirken ekranda **Çalıştır** düğmesini seçmeyi denerseniz görüntülenir. Tetiklemenin başlamak için çalıştırması gerekmez, Azure Işlevi dağıtıldığında otomatik olarak başlatılır. Azure portal Azure Işlevinin günlük akışını denetlemek istiyorsanız, izlenen kapsayıcınıza gitmeniz ve yeni öğeler eklemeniz yeterlidir. tetikleyiciyi otomatik olarak görürsünüz.

### <a name="my-changes-take-too-long-to-be-received"></a>Değişikliklerimin alınması çok uzun sürecek

Bu senaryoda birden çok neden olabilir ve bunların tümü denetlenmelidir:

1. Azure İşleviniz Azure Cosmos hesabınızla aynı bölgeye mi dağıtıldı? En iyi ağ gecikme süresi için hem Azure İşlevinin hem de Azure Cosmos hesabınızın aynı Azure bölgesinde birlikte bulunması gerekir.
2. Azure Cosmos kapsayıcınızda gerçekleşen değişiklikler sürekli mi yoksa aralıklı mı?
Aralıklıysa, değişikliklerin depolanması ile Azure İşlevinin bunları alması arasında biraz gecikme olabilir. Bunun nedeni dahili olarak tetikleyici Azure Cosmos kapsayıcınızda değişiklikleri denetleyip hiç okunmayı bekleyen değişiklik olmadığını bulduğunda, yeni değişiklikler için denetleme yapmadan önce yapılandırılabilir bir süre (varsayılan olarak 5 saniye) uykuya geçer (yüksek RU tüketimini önlemek için). Bu uyku süresini tetikleyicinizin [yapılandırmasındaki](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)`FeedPollDelay/feedPollDelay` ayarıyla yapılandırabilirsiniz (değerin milisaniye cinsinden olması beklenir).
3. Azure Cosmos Kapsayıcınız [hız sınırlı](./request-units.md)olabilir.
4. `PreferredLocations`Özel bir tercih edilen bağlantı sırası tanımlamak üzere Azure bölgelerinin virgülle ayrılmış bir listesini belirtmek için tetikleyicinizdeki özniteliğini kullanabilirsiniz.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Tetikleyicimde bazı değişiklikler yineleniyor

Bir "değişiklik" kavramı belge üzerinde bir işlemdir. Aynı belge için olayların alındığı en yaygın senaryolar şunlardır:
* Hesap nihai tutarlılığı kullanıyor. Değişiklik akışı nihai tutarlılık düzeyinde kullanılırken, sonraki değişiklik akışı okuma işlemleri arasında (bir okuma işleminin son olayı, sonraki ilk olarak görüntülenir) içinde yinelenen olaylar olabilir.
* Belge güncelleştiriliyor. Değişiklik akışı aynı belgeler için birden çok işlem içerebilir, bu belge güncelleştirmeleri alıyorsa, birden çok olay (her güncelleştirme için bir adet) alabilir. Aynı belge için farklı işlemler arasında ayrım yapmanın kolay bir yolu, `_lsn` [her bir değişikliğin özelliğini](change-feed.md#change-feed-and-_etag-_lsn-or-_ts)izlemedir. Bunlar eşleşmiyorsa, bunlar aynı belge üzerinde farklı değişikliklerdir.
* Belgeleri yalnızca tarafından tanımlıyor `id` , bir belgenin benzersiz tanımlayıcısının `id` ve bölüm anahtarı olduğunu ve (aynı `id` ancak farklı bölüm anahtarına sahip iki belge olabilir) olduğunu unutmayın.

### <a name="some-changes-are-missing-in-my-trigger"></a>Tetikleyicimde bazı değişiklikler yok

Azure Cosmos kapsayıcıınızda gerçekleşen bazı değişikliklerin Azure Işlevi tarafından çekilmediğini veya kopyalama sırasında hedefte bazı değişikliklerin eksik olduğunu fark ederseniz, lütfen aşağıdaki adımları izleyin.

Azure Işleviniz değişiklikleri aldığında, genellikle bunları işler ve isteğe bağlı olarak başka bir hedefe gönderebilirsiniz. Eksik değişiklikleri araştırırken, hedef üzerinde değil, alma noktasında (Azure Işlevi başladığında) **hangi değişikliklerin alındığını ölçdiğinizden** emin olun.

Hedefte bazı değişiklikler eksikse, bu, değişiklikler alındıktan sonra Azure Işlev yürütmesi sırasında bazı hatalar meydana geliyor olabilir.

Bu senaryoda, en iyi işlem `try/catch` konusu, kodunuzun içindeki blokları ve değişiklikleri işleyebilecek döngüleri eklemek, belirli bir öğe alt kümesi için herhangi bir hatayı tespit etmek ve bunları buna göre işlemek (daha fazla analiz veya yeniden denemek için başka bir depolama alanına göndermek).

> [!NOTE]
> Cosmos DB için Azure İşlevleri tetikleyicisi, kod yürütmeniz sırasında işlenmemiş bir özel durum oluştuğunda varsayılan olarak bir toplu değişiklik grubunu yeniden denemeyecektir. Bu, değişikliklerin hedefe ulaşamamasının nedeni, bunları İşleyemeyeceğiniz anlamına gelir.

Hedef başka bir Cosmos kapsayıcısıdır ve öğeleri kopyalamak için büyük işlemler uyguluyorsanız, **hem izlenen hem de hedef kapsayıcıdaki bölüm anahtarı tanımının aynı olduğunu doğrulayın**. Bu yapılandırma farkı nedeniyle, üst öğe, birden fazla kaynak öğesini hedefte tek bir kayıt halinde kaydediuyor.

Tetikleyicinizin tümünde bazı değişiklikler alınmadığından, en yaygın senaryo, **çalışan başka bir Azure işlevi** olduğundan emin olur. Bu, Azure 'da dağıtılan başka bir Azure Işlevi veya bir geliştirici makinesinde yerel olarak **aynı yapılandırmaya** (aynı izlenen ve kira kapsayıcıları) sahip olan bir Azure işlevi olabilir ve bu Azure Işlevi, Azure işlevinizin işlemesini beklediğiniz değişikliklerin bir alt kümesini çalmaya çalışır.

Ayrıca, kaç Azure İşlev Uygulaması örneğinin çalıştığını biliyorsanız senaryo doğrulanabilir. Kira kapsayıcınızı inceleyebilir ve içindeki kira öğelerinin sayısını saydıysanız, içindeki özelliğin farklı değerleri, `Owner` işlev uygulaması örneklerinin sayısına eşit olmalıdır. Bilinen Azure İşlev Uygulaması örneklerinden daha fazla sahip varsa, bu ek sahipler değişikliklerin "çalmasını" olduğu anlamına gelir.

Bu durumu çözmek için kolay bir yol, `LeaseCollectionPrefix/leaseCollectionPrefix` Yeni/farklı bir değere sahip işleviniz için bir veya yeni bir kiralama kapsayıcısı ile test etmek için geçerlidir.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Kapsayıcımda bulunan tüm öğelerin başlangıçtan itibaren yeniden başlatılması ve yeniden işlenmesi gerekiyor 
Bir kapsayıcıdaki tüm öğeleri başlangıçtan yeniden işlemek için:
1. Şu anda çalışıyorsa Azure işlevinizi durdurun. 
1. Kira koleksiyonundaki belgeleri silin (veya boş olması için kira koleksiyonunu silip yeniden oluşturun)
1. İşlevinizdeki [Startfromstarted](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) cosmosdbtrigger özniteliğini doğru olarak ayarlayın. 
1. Azure işlevini yeniden başlatın. Şimdi, başlangıçtan itibaren tüm değişiklikleri okur ve işler. 

[Startfromstart](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) 'ı true olarak ayarlamak, Azure işlevine geçerli saat yerine koleksiyonun geçmişinden başlayarak okuma işlemi başlatmasını bildirir. Bu yalnızca, zaten oluşturulan kiralamalar (yani, kiralamalar koleksiyonundaki belgeler) olmadığında işe yarar. Zaten oluşturulan kiralamalar varsa, bu özelliğin true olarak ayarlanması etkisizdir; Bu senaryoda, bir işlev durdurulduğunda ve yeniden başlatıldığında, kiralamalar koleksiyonunda tanımlandığı gibi son denetim noktasından okumaya başlar. Baştan sonra yeniden işlemek için yukarıdaki 1-4 adımları izleyin.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Bağlama yalnızca IReadOnlyList \<Document> veya JArray ile yapılabilir

Bu hata, Azure Işlevleri projeniz (veya başvurulan herhangi bir proje), [Azure işlevleri Cosmos DB uzantısı](./troubleshoot-changefeed-functions.md#dependencies)tarafından sağlanenden farklı bir SÜRÜMLE Azure Cosmos DB SDK 'ya el ile NuGet başvurusu içeriyorsa meydana gelir.

Bu durumu geçici olarak çözmek için eklenen el ile NuGet başvurusunu kaldırın ve Azure Cosmos DB SDK başvurusunun Azure Işlevleri Cosmos DB Uzantı paketi aracılığıyla çözümlendiğine izin verin.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Değişikliklerin algılanması için Azure Işlevinin yoklama aralığı değiştiriliyor

Daha önce [değişikliklerinizin alınması çok uzun sürme](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)için, Azure işlevi yeni değişiklikler denetlenmeden önce yapılandırılabilir bir süre (varsayılan olarak 5 saniye) için uykuya geçecek (yüksek ru tüketimine engel olmak için). Bu uyku süresini tetikleyicinizin [yapılandırmasındaki](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)`FeedPollDelay/feedPollDelay` ayarıyla yapılandırabilirsiniz (değerin milisaniye cinsinden olması beklenir).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Işlevleriniz için izlemeyi etkinleştirme](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK sorunlarını giderme](./troubleshoot-dot-net-sdk.md)

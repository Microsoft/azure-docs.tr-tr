---
title: Azure Cosmos DB içindeki saklı yordamlar, Tetikleyiciler ve UDF 'ler ile çalışma
description: Bu makalede, Azure Cosmos DB saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevler gibi kavramlar tanıtılmaktadır.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: ad9e6b99b396465c2cff95bd6ab340ef9d668085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575966"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevler
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB JavaScript'in dil ile tümleşik, işlem tabanlı yürütmesini sağlar. Azure Cosmos DB içinde SQL API 'sini kullanırken, JavaScript dilinde **saklı yordamlar**, **Tetikleyiciler** ve **Kullanıcı tanımlı işlevler (UDF 'ler)** yazabilirsiniz. Veritabanı altyapısı içinde yürütülen mantığınızı JavaScript'te yazabilirsiniz. Azure Cosmos DB veya [Cosmos db SQL API Istemci SDK](how-to-use-stored-procedures-triggers-udfs.md)'larının [Azure Portal](https://portal.azure.com/), [JavaScript Dil tümleşik sorgu API 'sini](javascript-query-api.md) kullanarak Tetikleyiciler, saklı yordamlar ve UDF 'ler oluşturabilir ve bunları çalıştırabilirsiniz.

## <a name="benefits-of-using-server-side-programming"></a>Sunucu tarafı programlama kullanmanın avantajları

JavaScript 'te saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevler (UDF 'ler) yazmak, zengin uygulamalar oluşturmanıza olanak tanır ve aşağıdaki avantajları sağlar:

* **Yordamsal Logic:** İş mantığını ifade etmek için zengin ve tanıdık arabirim sağlayan üst düzey bir programlama dili olarak JavaScript. Veriler üzerinde bir dizi karmaşık işlem gerçekleştirebilirsiniz.

* **Atomik işlemler:** Tek bir saklı yordamda veya bir tetikleyicide gerçekleştirilen Azure Cosmos DB veritabanı işlemleri atomik. Bu atomik işlevsellik, bir uygulamanın tüm işlemlerin başarılı veya hiç olmaması için ilgili işlemleri tek bir toplu işte birleştirmesine olanak tanır.

* **Performans:** JSON verileri, JavaScript dil türü sistemine doğası gereği eşlenir. Bu eşleme, arabellek havuzundaki JSON belgelerinin geç şekilde kullanıma hazır hale getirilmesi ve bunları yürütülen koda talep üzerine getirmek gibi çeşitli iyileştirmeler sağlar. İş mantığını veritabanına aktarma ile ilgili başka performans avantajları vardır; buna şunlar dahildir:

   * *Toplu işleme:* Ekleme gibi işlemleri toplu olarak gruplandırabilir ve toplu olarak gönderebilirsiniz. Ağ trafiği gecikme maliyetleri ve ayrı işlemler oluşturmaya yönelik mağaza ek yükü önemli ölçüde azaltılır.

   * *Ön derleme:* Saklı yordamlar, Tetikleyiciler ve UDF 'ler, her bir betik çağırma sırasında derleme maliyetinden kaçınmak için, bayt kodu biçiminde örtük olarak önceden derlenir. Ön derleme nedeniyle, saklı yordamların çağrılması hızlıdır ve düşük bir ayak izi vardır.

   * *Sıralama:* Bazen işlemlere bir veya daha fazla güncelleştirme gerçekleştirebilen tetikleyen bir mekanizmaya ihtiyacı vardır. Atomicity 'in yanı sıra sunucu tarafında yürütme sırasında da performans avantajları da vardır.

* **Kapsülleme:** Saklı yordamlar, mantığı tek bir yerde gruplamak için kullanılabilir. Kapsülleme, verilerin üzerine bir soyutlama katmanı ekler ve bu sayede uygulamalarınızı verilerden bağımsız olarak geliştirebilirsiniz. Bu soyutlama katmanı, veriler şemaya göre daha az olduğunda ve doğrudan uygulamanıza ek mantık eklemeyi yönetmeniz gerekmiyorsa yararlı olur. Soyutlama, betiklerin erişimini kolaylaştırarak verilerin güvenliğini korumanıza olanak sağlar.

> [!TIP]
> Saklı yordamlar, yazma açısından ağır ve bölüm anahtarı değerinde bir işlem gerektiren işlemler için idealdir. Saklı yordamların kullanılıp kullanılmayacağını saptarken, mümkün olan en fazla yazma miktarını kapsülleyerek iyileştirin. Genel anlamda, saklı yordamlar çok sayıda okuma veya sorgu işlemi yapmak için en etkili yol değildir, bu nedenle istemciye geri dönmek için çok sayıda okuma işlemi uygulamak için saklı yordamları kullanmak istenen avantaja sahip olmayacaktır. En iyi performansı elde etmek için, bu okuma ağır işlemleri Cosmos SDK kullanılarak istemci tarafında yapılmalıdır. 

## <a name="transactions"></a>İşlemler

Tipik bir veritabanındaki işlem, tek bir mantıksal iş birimi olarak gerçekleştirilen işlem sırası olarak tanımlanabilir. Her işlem, **ACID Özellik garantisi** sağlar. ACID, şunu temsil eden iyi bilinen bir kısaltmadır: **a**-micity, **C** onsistency, **i** solation ve **D** uryeteneğinin. 

* Atomicity, bir işlem içinde gerçekleştirilen tüm işlemlerin tek bir birim olarak değerlendirildiğinden ve tümünün yürütüldüğünden ya da hiçbirinin olmadığından emin garanti eder. 

* Tutarlılık, verilerin işlemler arasında her zaman geçerli bir durumda olduğundan emin olmanızı sağlar. 

* Yalıtım, birbirleriyle iki işlemin kesintiye uğratılmasını garanti eder; birçok ticari sistem, uygulama ihtiyaçlarına göre kullanılabilecek birden fazla yalıtım düzeyi sağlar. 

* Dayanıklılık, bir veritabanında kaydedilen tüm değişikliklerin her zaman mevcut olmasını sağlar.

Azure Cosmos DB, JavaScript çalışma zamanı veritabanı altyapısının içinde barındırılır. Bu nedenle, saklı yordamlar içinde yapılan istekler ve Tetikleyiciler veritabanı oturumuyla aynı kapsamda yürütülür. Bu özellik, Azure Cosmos DB saklı yordamın veya tetikleyicinin parçası olan tüm işlemler için ACID özelliklerini güvence altına almasını sağlar. Örnekler için bkz. [işlem nasıl uygulanır](how-to-write-stored-procedures-triggers-udfs.md#transactions) makalesi.

### <a name="scope-of-a-transaction"></a>Bir işlemin kapsamı

Saklı yordamlar bir Azure Cosmos kapsayıcısı ile ilişkilendirilir ve saklı yordam yürütme, mantıksal bir bölüm anahtarına göre kapsamlandırılır. Saklı yordamlar, yürütme sırasında işlem kapsamı için mantıksal bölümü tanımlayan bir mantıksal bölüm anahtarı değeri içermelidir. Daha fazla bilgi için bkz. [Azure Cosmos DB bölümlendirme](partitioning-overview.md) makalesi.

### <a name="commit-and-rollback"></a>Yürüt ve geri al

İşlemler Azure Cosmos DB JavaScript programlama modeliyle yerel olarak tümleşiktir. Bir JavaScript işlevi içinde, tüm işlemler otomatik olarak tek bir işlem altında sarmalanır. Saklı yordamdaki JavaScript mantığı herhangi bir özel durum olmadan tamamlanırsa, işlem içindeki tüm işlemler veritabanına işlenir. `BEGIN TRANSACTION`Ve `COMMIT TRANSACTION` (ilişkisel veritabanlarına tanıdık) gibi deyimler örtük Azure Cosmos DB. Betikten özel durum varsa Azure Cosmos DB JavaScript çalışma zamanı işlemin tamamını geri alacak. Bu nedenle, bir özel durum oluşturmak Azure Cosmos DB bir içinde etkin bir şekilde eşdeğerdir `ROLLBACK TRANSACTION` .

### <a name="data-consistency"></a>Veri tutarlılığı

Saklı yordamlar ve Tetikleyiciler, her zaman bir Azure Cosmos kapsayıcısının birincil çoğaltmasında yürütülür. Bu özellik, saklı yordamlardan gelen okumaların [güçlü tutarlılık](./consistency-levels.md)sunmasını sağlar. Kullanıcı tanımlı işlevleri kullanan sorgular birincil veya herhangi bir ikincil çoğaltmada yürütülebilir. Saklı yordamlar ve Tetikleyiciler işlem yazma işlemlerini desteklemek için tasarlanmıştır: salt okuma mantığı, [Azure Cosmos db SQL API SDK 'ları](sql-api-dotnet-samples.md)kullanarak uygulama tarafı mantığı ve sorgular olarak en iyi şekilde uygulandığından, veritabanı aktarım hızını ortadan kaldırmaya yardımcı olur. 

> [!TIP]
> Saklı yordam veya tetikleyici içinde yürütülen sorgular, aynı betik işlemi tarafından yapılan öğelerde yapılan değişiklikleri göremeyebilir. Bu ifade `getContent().getCollection.queryDocuments()` , ve gibi tümleşik dil sorgularının yanı sıra SQL sorgularına de uygulanır `getContext().getCollection().filter()` .

## <a name="bounded-execution"></a>Sınırlanmış yürütme

Tüm Azure Cosmos DB işlemleri belirtilen zaman aşımı süresi içinde tamamlanmalıdır. Saklı yordamların 5 saniyelik bir zaman aşımı sınırı vardır. Bu kısıtlama JavaScript işlevlerine saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevler için geçerlidir. İşlem bu zaman sınırı içinde tamamlanmazsa, işlem geri alınır.

JavaScript işlevlerinizin zaman sınırı içinde bitmesini veya yürütmeyi toplu olarak çalıştırmaya devam etmek için devamlılık tabanlı bir model uygulayıp uygulamamayı sağlayabilirsiniz. Zaman sınırlarını işlemek üzere saklı yordamların ve tetikleyicilerin geliştirilmesini basitleştirmek için, Azure Cosmos kapsayıcısı kapsamındaki tüm işlevler (örneğin, öğe oluşturma, okuma, güncelleştirme ve silme), bu işlemin tamamlanıp tamamlanmayacağını temsil eden bir Boole değeri döndürür. Bu değer false ise, komut dosyası, yapılandırılan değerden daha fazla zaman veya sağlanan aktarım hızı kullandığından yordamın yürütmeyi sarması gerektiğini belirtir. İlk kabul edilmeyen depolama işleminden önce sıraya alınan işlemler, saklı yordam zamanında tamamlanırsa ve daha fazla istek sıraya almaz. Bu nedenle, komut dosyasının denetim akışını yönetmek için JavaScript 'in geri çağırma kuralı kullanılarak işlemler tek seferde sıraya alınmalıdır. Betikler sunucu tarafı bir ortamda yürütüldüğü için, bunlar kesinlikle yönetilir. Yürütme sınırlarını sürekli ihlal eden betikler etkin değil olarak işaretlenebilir ve yürütme sınırlarını dikkate almak için bunların yeniden oluşturulması gerekir.

JavaScript işlevleri, [sağlanan verimlilik kapasitesine](request-units.md)de tabidir. JavaScript işlevleri kısa bir süre içinde çok sayıda istek birimi kullanarak bitebilecek ve sağlanan üretilen iş kapasitesi sınırına ulaşıldığında hız sınırlı olabilir. Bu veritabanı işlemlerinin, istemciden aynı işlemleri yürütmekten biraz daha ucuz olmasına karşın, betiklerin veritabanı işlemlerini yürütürken harcanan aktarım hızına ek olarak ek aktarım hızı tükettiği unutulmamalıdır.

## <a name="triggers"></a>Tetikleyiciler

Azure Cosmos DB iki tür tetikleyiciyi destekler:

### <a name="pre-triggers"></a>Ön tetikleyiciler

Azure Cosmos DB, bir Azure Cosmos öğesinde işlem gerçekleştirerek çağrılabilen tetikleyiciler sağlar. Örneğin, öğe oluştururken bir ön tetikleyici belirtebilirsiniz. Bu durumda öğe oluşturulmadan önce ön tetikleyici çalıştırılır. Ön tetikleyicilerin hiçbir giriş parametresi olamaz. Gerekirse özgün istekten belge gövdesini güncelleştirmek için istek nesnesi kullanılabilir. Tetikleyiciler kaydedilirken kullanıcılar birlikte çalıştırılabileceği işlemleri belirtebilir. Tetikleyici `TriggerOperation.Create` ile oluşturulduysa, bu tetikleyicinin bir değiştirme işleminde kullanılmasına izin verilmez. Örnekler için bkz. [tetikleyici yazma](how-to-write-stored-procedures-triggers-udfs.md#triggers) makalesi.

### <a name="post-triggers"></a>Son tetikleyiciler

Ön Tetikleyiciler, tetikleyicilere benzer şekilde Azure Cosmos öğesindeki bir işlemle da ilişkilendirilir ve herhangi bir giriş parametresi gerekmez. İşlem tamamlandıktan *sonra* çalışır ve istemciye gönderilen yanıt iletisine erişim sağlar. Örnekler için bkz. [tetikleyici yazma](how-to-write-stored-procedures-triggers-udfs.md#triggers) makalesi.

> [!NOTE]
> Kayıtlı Tetikleyiciler, ilgili işlemler (oluşturma/silme/değiştirme/güncelleştirme) gerçekleştiğinde otomatik olarak çalıştırılmaz. Bu işlemler yürütürken tetikleyicilerin açıkça çağrılması gerekir. Daha fazla bilgi için bkz. [Tetikleyicileri çalıştırma](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) makalesi.

## <a name="user-defined-functions"></a><a id="udfs"></a>Kullanıcı tanımlı işlevler

[Kullanıcı tanımlı işlevler](sql-query-udfs.md) (UDF 'ler), SQL API sorgu dili sözdizimini genişletmek ve özel iş mantığını kolayca uygulamak için kullanılır. Bunlar yalnızca sorgular içinde çağrılabilir. UDF 'ler bağlam nesnesine erişemez ve yalnızca işlem JavaScript olarak kullanılmak üzere tasarlanmıştır. Bu nedenle, UDF 'ler ikincil çoğaltmalar üzerinde çalıştırılabilir. Örnekler için bkz. [Kullanıcı tanımlı işlevleri yazma](how-to-write-stored-procedures-triggers-udfs.md#udfs) makalesi.

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>JavaScript dil ile tümleşik sorgu API 'SI

SQL API sorgu söz dizimini kullanarak sorgu vermekten ek olarak, [sunucu tarafı SDK 'sı](https://azure.github.io/azure-cosmosdb-js-server) herhangi bir bilginiz olmadan bir JavaScript arabirimi kullanarak sorgular gerçekleştirmenize olanak tanır. JavaScript sorgu API 'SI, koşul işlevlerini işlev çağrıları dizisine geçirerek programlı bir şekilde sorgu oluşturmanızı sağlar. Sorgular JavaScript çalışma zamanı tarafından ayrıştırılır ve Azure Cosmos DB içinde verimli bir şekilde yürütülür. JavaScript sorgu API 'SI desteği hakkında bilgi edinmek için bkz. [JavaScript Dil tümleşik sorgu API 'si Ile çalışma](javascript-query-api.md) . Örnekler için bkz. [JavaScript sorgu API 'si kullanılarak saklı yordamları ve Tetikleyicileri yazma](how-to-write-javascript-query-api.md) .

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerle Azure Cosmos DB saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri nasıl yazıp kullanacağınızı öğrenin:

* [Saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri yazma](how-to-write-stored-procedures-triggers-udfs.md)

* [Saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri kullanma](how-to-use-stored-procedures-triggers-udfs.md)

* [JavaScript Dil tümleşik sorgu API 'SI ile çalışma](javascript-query-api.md)
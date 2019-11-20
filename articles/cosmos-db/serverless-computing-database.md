---
title: Sunucusuz veritabanı hesaplama-Azure Işlevleri ve Azure Cosmos DB
description: Azure Cosmos DB ve Azure Işlevlerinin nasıl birlikte kullanılabileceğini, olay odaklı sunucusuz bilgi işlem uygulamaları oluşturmayı öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: e1014c710d892e45f09999db22b1f59c0bb36300
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614590"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB ve Azure Işlevleri 'ni kullanarak sunucusuz veritabanı hesaplama

Sunucusuz bilgi işlem, tekrarlanabilir ve durumsuz olan ayrı mantık parçalarına odaklanma imkanına sahiptir. Bu parçalar altyapı yönetimi gerektirmez ve kaynakları yalnızca, için çalıştıkları saniyeler veya milisaniyelerle tüketir. Sunucusuz bilgi işlem hareketinin çekirdeğiyle Azure 'un Azure [işlevleri](https://azure.microsoft.com/services/functions)tarafından sunulan Işlevleri, Azure ekosisteminde kullanılabilir. Azure 'da diğer sunucusuz yürütme ortamları hakkında daha fazla bilgi edinmek için bkz. [Azure 'da sunucusuz](https://azure.microsoft.com/solutions/serverless/) sayfasında. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) Ile Azure işlevleri arasında yerel tümleştirmeyle, doğrudan Azure Cosmos DB hesabınızdan veritabanı Tetikleyicileri, giriş bağlamaları ve çıkış bağlamaları oluşturabilirsiniz. Azure Işlevleri ve Azure Cosmos DB kullanarak, genel bir kullanıcı tabanı için zengin verilere yönelik düşük gecikmeli erişimle olay odaklı sunucusuz uygulamalar oluşturabilir ve dağıtabilirsiniz.

## <a name="overview"></a>Genel Bakış

Azure Cosmos DB ve Azure Işlevleri, veritabanlarınızı ve sunucusuz uygulamalarınızı aşağıdaki yollarla tümleştirmenizi sağlar:

* Cosmos DB için olay odaklı bir **Azure işlevleri tetikleyicisi**oluşturun. Bu tetikleyici, değişiklikler için Azure Cosmos kapsayıcınızı izlemek üzere [değişiklik akışı](change-feed.md) akışlarına bağımlıdır. Bir kapsayıcıda değişiklik yapıldığında, değişiklik akışı akışı, Azure Işlevini çağıran tetikleyicisine gönderilir.
* Alternatif olarak, bir Azure Işlevini bir **giriş bağlama**kullanarak bir Azure Cosmos kapsayıcısına bağlayın. Giriş bağlamaları bir işlev yürütüldüğünde bir kapsayıcıdan veri okur.
* **Çıkış bağlamayı**kullanarak bir Işlevi Azure Cosmos kapsayıcısına bağlayın. Çıkış bağlamaları bir işlev tamamlandığında bir kapsayıcıya veri yazar.

> [!NOTE]
> Şu anda, Cosmos DB için Azure Işlevleri tetikleyicisi, giriş bağlamaları ve çıkış bağlamaları yalnızca SQL API 'SI ile kullanım için desteklenir. Diğer tüm Azure Cosmos DB API 'Lerinde, API 'niz için statik istemciyi kullanarak işlevinizden veritabanına erişmeniz gerekir.


Aşağıdaki diyagramda bu üç tümleştirmelerin her biri gösterilmektedir: 

![Azure Cosmos DB ve Azure Işlevleri nasıl tümleştirilir?](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Cosmos DB için Azure Işlevleri tetikleyicisi, giriş bağlama ve çıkış bağlaması aşağıdaki bileşimlerde kullanılabilir:

* Cosmos DB için bir Azure Işlevleri tetikleyicisi, farklı bir Azure Cosmos kapsayıcısına çıkış bağlaması ile kullanılabilir. Bir işlev değişiklik akışındaki bir öğe üzerinde bir eylem gerçekleştirdikten sonra, bunu başka bir kapsayıcıya yazabilirsiniz (bunun, geldiği aynı kapsayıcıya yazmak etkin bir yinelemeli döngü oluşturur). Ya da, değişen tüm öğeleri bir kapsayıcıdan farklı bir kapsayıcıya, çıkış bağlamasının kullanımıyla etkin bir şekilde geçirmek için Cosmos DB için bir Azure Işlevleri tetikleyicisi kullanabilirsiniz.
* Azure Cosmos DB için giriş bağlamaları ve çıkış bağlamaları aynı Azure Işlevinde kullanılabilir. Bu, giriş bağlaması ile belirli verileri bulmak, Azure Işlevinde değiştirmek ve ardından değişikliği sonrasında aynı kapsayıcıya veya farklı bir kapsayıcıya kaydetmek istediğinizde iyi çalışır.
* Azure Cosmos kapsayıcısına bir giriş bağlama, Cosmos DB için bir Azure Işlevleri tetikleyicisi ile aynı işlevde kullanılabilir ve ayrıca, çıkış bağlamasıyla veya bunlarla birlikte kullanılabilir. Bu bileşimi, güncel para birimi değişim bilgilerini (bir Exchange kapsayıcısına bir giriş bağlamasıyla çekilir), alışveriş sepeti hizmetinizde yeni siparişlerin değişiklik akışına uygulamak için kullanabilirsiniz. Güncel para birimi dönüştürme uygulanmış olan güncelleştirilmiş alışveriş sepeti toplamı, bir çıkış bağlaması kullanılarak üçüncü bir kapsayıcıya yazılabilir.

## <a name="use-cases"></a>Uygulama alanları

Aşağıdaki kullanım örnekleri, verilerinizi olay odaklı Azure Işlevlerine bağlayarak Azure Cosmos DB verilerinizin en iyi şekilde yapabilmenizin birkaç yolunu göstermektedir.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT kullanım örneği-Cosmos DB için Azure Işlevleri tetikleme ve çıkış bağlama

IoT uygulamalarında, bağlı bir otomobilde Check Engine ışığı görüntülenirken bir işlevi çağırabilirsiniz.

**Paylaşır** Cosmos DB için bir Azure Işlevleri tetikleyicisi ve çıkış bağlaması kullanın

1. **Cosmos DB için bir Azure işlevleri tetikleyicisi** , bağlı bir arabada gelen denetim motoru ışığı gibi oto uyarıları ile ilgili olayları tetiklemek için kullanılır.
2. Check Engine ışığı geldiğinde, algılayıcı verileri Azure Cosmos DB gönderilir.
3. Azure Cosmos DB yeni algılayıcı veri belgeleri oluşturur veya güncelleştirir, bu değişiklikler Cosmos DB için Azure Işlevleri tetikleyicisine akışla kaydedilir.
4. Tetikleyici, değişiklik akışı aracılığıyla tüm değişiklikler akışı yaptığı için, algılayıcı veri koleksiyonundaki her veri değişikliği üzerinde çağrılır.
5. Bir eşik koşulu, algılayıcı verilerini garanti departmanına göndermek için işlevinde kullanılır.
6. Sıcaklık aynı zamanda belirli bir değer de varsa, sahibine da bir uyarı gönderilir.
7. İşlevindeki **Çıkış bağlaması** , diğer bir Azure Cosmos kapsayıcısındaki araba kaydını, Check Engine olayı hakkında bilgi depolamak için güncelleştirir.

Aşağıdaki görüntüde, bu tetikleyici için Azure portal yazılan kod gösterilmektedir.

![Azure portal Cosmos DB için bir Azure Işlevleri tetikleyicisi oluşturun](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finansal kullanım örneği-Zamanlayıcı tetikleyicisi ve giriş bağlama

Mali uygulamalarda, banka hesabı bakiyesi belirli bir miktarın altına düştüğünde bir işlevi çağırabilirsiniz.

**Paylaşır** Azure Cosmos DB girişi bağlamaya sahip bir Zamanlayıcı tetikleyicisi

1. Bir [Zamanlayıcı tetikleyicisi](../azure-functions/functions-bindings-timer.md)kullanarak bir Azure Cosmos kapsayıcısında depolanan banka hesap bakiyesi bilgilerini, bir **giriş bağlaması**kullanarak zaman aralıklarıyla alabilirsiniz.
2. Bakiye, Kullanıcı tarafından ayarlanan düşük bakiyeli eşiğin altındaysa Azure Işlevindeki bir eylemle takip edin.
3. Çıkış bağlama, bir hizmet hesabından bir e-posta gönderen bir [SendGrid tümleştirmesi](../azure-functions/functions-bindings-sendgrid.md) olabilir ve bu, düşük bilanço hesaplarının her biri için tanımlanan e-posta adreslerine.

Aşağıdaki resimlerde, bu senaryonun Azure portal kodu gösterilmektedir.

![Finansal senaryo için bir Zamanlayıcı tetikleyicisi için Index. js dosyası](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Finansal senaryo için bir Zamanlayıcı tetikleyicisi için. CSX dosyasını çalıştır](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Oyun kullanım örneği-Cosmos DB için Azure Işlevleri tetikleme ve çıkış bağlama 

Oyun ' de yeni bir kullanıcı oluşturulduğunda, [Azure Cosmos DB Gremlin API](graph-introduction.md)'sini kullanarak bunları bilen diğer kullanıcıları arayabilirsiniz. Daha sonra kolayca almak için sonuçları bir [Azure Cosmos DB SQL veritabanına] yazabilirsiniz.

**Paylaşır** Cosmos DB için bir Azure Işlevleri tetikleyicisi ve çıkış bağlaması kullanın

1. Tüm kullanıcıları depolamak için bir Azure Cosmos DB [Graph veritabanı](graph-introduction.md) kullanarak, Cosmos DB Için Azure işlevleri tetikleyicisiyle yeni bir işlev oluşturabilirsiniz. 
2. Her yeni kullanıcı eklendiğinde, işlev çağrılır ve sonuç bir **Çıkış bağlaması**kullanılarak depolanır.
3. İşlevi, yeni kullanıcıyla doğrudan ilgili tüm kullanıcıları aramak için grafik veritabanını sorgular ve bu veri kümesini işleve döndürür.
4. Bu veriler daha sonra, yeni kullanıcıyı bağlı arkadaşlarını gösteren bir ön uç uygulama tarafından kolayca alınabilecek bir Azure Cosmos DB depolanır.

### <a name="retail-use-case---multiple-functions"></a>Perakende kullanım örneği-birden çok işlev

Perakende uygulamalarında, bir Kullanıcı sepetine bir öğe eklediğinde, artık isteğe bağlı iş ardışık düzen bileşenleri için işlev oluşturma ve çağırma esnekliği vardır.

**Paylaşır** Birden çok Azure Işlevi, tek bir kapsayıcıya dinleme yapan Cosmos DB tetikler

1. Her birine Cosmos DB için Azure Işlevleri tetikleyicisi ekleyerek, alışveriş sepeti verilerinin aynı değişiklik akışını dinleyerek birden çok Azure Işlevi oluşturabilirsiniz. Birden çok işlev aynı değişiklik akışını dinlerken, her bir işlev için yeni bir kira koleksiyonu gerektiğini unutmayın. Kira koleksiyonları hakkında daha fazla bilgi için bkz. [değişiklik akışı işlemci kitaplığını anlama](change-feed-processor.md).
2. Bir Kullanıcı alışverişi sepetine her yeni öğe eklendiğinde, her bir işlev, alışveriş sepeti kapsayıcısından gelen değişiklik akışı tarafından bağımsız olarak çağrılır.
   * Bir işlev, kullanıcının ilgilendiği diğer öğelerin görüntüsünü değiştirmek için geçerli sepetin içeriğini kullanabilir.
   * Başka bir işlev, envanter toplamlarını güncelleştirebilir.
   * Başka bir işlev, belirli ürünler için müşteri bilgilerini bir promosyon gönderisini gönderen Pazarlama departmanına gönderebilir. 

     Herhangi bir departman, değişiklik akışını dinleyerek Cosmos DB için bir Azure Işlevleri oluşturabilir ve işlemdeki önemli sıra işleme olaylarını geciktiremayadıklarından emin olun.

Bu kullanım örneklerinin tümünde, işlev uygulamanın kendisini ayırdığından, her zaman yeni uygulama örnekleri almanız gerekmez. Bunun yerine, Azure Işlevleri ayrı işlemleri gerektiği şekilde tamamlamaya yönelik işlevleri tek tek döner.

## <a name="tooling"></a>Araçları

Azure Cosmos DB ve Azure Işlevleri arasında yerel tümleştirme, Azure portal ve Visual Studio 2019 ' de mevcuttur.

* Azure Işlevleri portalında bir tetikleyici oluşturabilirsiniz. Hızlı başlangıç yönergeleri için bkz. [Azure portal Cosmos DB Için Azure işlevleri tetikleyicisi oluşturma](https://aka.ms/cosmosdbtriggerportalfunc).
* Azure Cosmos DB portalında, aynı kaynak grubundaki mevcut bir Azure Işlev uygulamasına Cosmos DB için bir Azure Işlevleri tetikleyicisi ekleyebilirsiniz.
* Visual Studio 2019 ' de, [Azure Işlevleri araçlarını](../azure-functions/functions-develop-vs.md)kullanarak tetikleyiciyi oluşturabilirsiniz:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Sunucusuz bilgi işlem için neden Azure Işlevleri tümleştirmesi seçmelisiniz?

Azure Işlevleri, altyapının sağlanması veya yönetilmesi gerekmeden, ölçeklenebilir iş birimleri veya isteğe bağlı olarak çalışabilen kısa mantık parçalarını oluşturma olanağı sağlar. Azure Işlevleri 'ni kullanarak, Azure Cosmos veritabanınızdaki değişikliklere yanıt vermek için tam bir blown uygulaması oluşturmanız gerekmez, belirli görevler için küçük yeniden kullanılabilir işlevler oluşturabilirsiniz. Ayrıca, HTTP istekleri veya süreli tetikleyici gibi bir olaya yanıt olarak Azure Işlevine giriş veya çıkış olarak Azure Cosmos DB verilerini de kullanabilirsiniz.

Azure Cosmos DB, aşağıdaki nedenlerden dolayı sunucusuz bilgi işlem mimariniz için önerilen veritabanıdır:

* **Verilerinize anında erişin**: Tüm verileri varsayılan olarak [otomatik olarak dizinleyen](index-policy.md) ve bu dizinleri hemen kullanılabilir hale getiren her değere ayrıntılı olarak erişebilirsiniz Azure Cosmos DB. Bu, veritabanınıza sürekli olarak sorgu, güncelleştirme ve yeni öğe ekleme ve Azure Işlevleri aracılığıyla anında erişim sağlayabilmeniz anlamına gelir.

* **Şeless**. Azure Cosmos DB şesız olduğundan, bir Azure Işlevinden alınan tüm veri çıkışlarını benzersiz bir şekilde işleyebiliyor. Bu "her şeyi işle" yaklaşımı, tüm çıktının Azure Cosmos DB için bir bütün olarak çeşitli Işlevler oluşturmayı basit hale getirir.

* **Ölçeklenebilir üretilen iş**. Verimlilik, Azure Cosmos DB anında yukarı ve aşağı ölçeklendirilebilir. Aynı kapsayıcıya sorgu ve yazma yüzlerce veya binlerce Işlevleriniz varsa, yükü işlemek için [ru/s](request-units.md) 'nizi ölçeklendirebilirsiniz. Tüm işlevler, ayrılmış RU/s 'niz kullanılarak paralel çalışabilir ve verilerinizin [tutarlı](consistency-levels.md)olması garanti edilir.

* **Genel çoğaltma**. [Dünyayı etrafında](distribute-data-globally.md) Azure Cosmos DB verileri çoğaltıp, verilerinizi kullanıcılarınızın bulunduğu yere en yakın şekilde coğrafi olarak bulabilirsiniz. Tüm Azure Cosmos DB sorgularında olduğu gibi, olay odaklı tetikleyicilerden gelen veriler, kullanıcıya en yakın Azure Cosmos DB okunan verileri okur.

Verileri depolamak ve derin dizin oluşturmak için Azure Işlevleriyle tümleştiriyorsanız veya ekleri ve medya dosyalarını depolamanız gerekiyorsa, [Azure Blob depolama tetikleyicisi](../azure-functions/functions-bindings-storage-blob.md) daha iyi bir seçenek olabilir.

Azure Işlevlerinin avantajları: 

* **Olay odaklı**. Azure Işlevleri olay odaklı ve Azure Cosmos DB bir değişiklik akışını dinleyebilir. Bu, dinleme mantığı oluşturmanız gerekmediği anlamına gelir, yalnızca dinleki yaptığınız değişiklikler için göz önünde tutmanız yeterlidir. 

* **Sınırsız**. İşlevler paralel olarak yürütülür ve hizmet, gerek duyduğunuz kadar fazla döner. Parametreleri ayarlarsınız.

* **Hızlı Görevler Için iyi**. Hizmet, bir olay her tetiklendiğinde yeni işlev örneklerini alır ve işlev tamamlanır almaz bunları kapatır. Yalnızca işlevlerinizin çalıştığı süre için ödeme yaparsınız.

Flow, Logic Apps, Azure Işlevleri veya Web Işlerinin uygulamanız için en iyi şekilde olup olmadığından emin değilseniz, bkz. [Flow, Logic Apps, işlevler ve Web işleri arasında seçim](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)yapın.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Azure Cosmos DB ve Azure Işlevlerine gerçek zamanlı olarak bağlanalım: 

* [Azure portal Cosmos DB için bir Azure Işlevleri tetikleyicisi oluşturun](https://aka.ms/cosmosdbtriggerportalfunc)
* [Azure Cosmos DB girişi bağlaması ile Azure Işlevleri HTTP tetikleyicisi oluşturma](https://aka.ms/cosmosdbinputbind)
* [Bağlamalar ve Tetikleyiciler Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md)


 




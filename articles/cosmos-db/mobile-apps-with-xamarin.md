---
title: 'Öğretici: Xamarin ve Azure Cosmos DB ile mobil uygulamalar oluşturun'
description: 'Öğretici: Azure Cosmos DB kullanarak bir Xamarin iOS, Android veya Forms uygulaması oluşturan öğretici. Azure Cosmos DB mobil uygulamalar için hızlı, dünya çapında ölçeklenebilen bir bulut veritabanıdır.'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: d1164c14b406fb66638e6a5333fe38180272aa14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359345"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Öğretici: Xamarin ve Azure Cosmos DB ile mobil uygulamalar oluşturun
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Çoğu mobil uygulamanın bulutta veri depolaması gerekir ve Azure Cosmos DB mobil uygulamalar için bir bulut veritabanıdır. Mobil geliştirici ihtiyacı olan her şeye sahiptir. İsteğe bağlı olarak ölçeklenen, tam olarak yönetilen bir hizmet olarak veritabanıdır. Kullanıcılarınız dünyada nerede bulunursa bulunsun verilerinizi uygulamanızda saydam bir şekilde kullanıma sunabilirsiniz. [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md)’yi kullanarak Xamarin mobil uygulamalarının orta katman olmadan Azure Cosmos DB ile doğrudan etkileşim kurmasını sağlayabilirsiniz.

Bu makalede, Xamarin ve Azure Cosmos DB ile mobil uygulamalar oluşturma öğreticisi yer almaktadır. Kullanıcıları ve izinleri yönetme de dahil olmak üzere öğreticinin tam kaynak kodunu [GitHub'da Xamarin ve Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) altında bulabilirsiniz.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Mobil uygulamalar için Azure Cosmos DB özellikleri
Azure Cosmos DB mobil uygulama geliştiricileri için aşağıdaki anahtar yetenekleri sağlar:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-for-mobile.png" alt-text="Mobil uygulamalar için Azure Cosmos DB özellikleri":::

* Şemasız veriler üzerinde zengin sorgular. Azure Cosmos DB verileri heterojen koleksiyonlarda şemasız JSON belgeleri olarak depolar. Şemalar veya dizinler hakkında endişelenmeye gerek kalmadan [zengin ve hızlı sorguları](./sql-query-getting-started.md) sunar.
* İşlem hızı yüksektir. Azure Cosmos DB ile belgeleri okumak ve yazmak yalnızca birkaç milisaniye alır. Geliştiriciler ihtiyaçları olan işlem hızını belirtebilir ve Azure Cosmos DB, rahat bir tutarlılıkla tek tek tüm bölge hesapları ve çok bölgeli tüm hesaplar için %99,99 kullanılabilirlik SLA'sı ve çok bölgeli tüm veritabanı hesaplarında %99,999 okunabilirlik sağlar.
* Sınırsız ölçek. [Uygulamanız büyüdükçe](partitioning-overview.md)Azure Cosmos kapsayıcılarınız büyür. Küçük veri boyutu ve saniye başına yüzlerce istekle başlayabilirsiniz. Koleksiyonlarınız veya veritabanlarınız zaman içinde petabaytlarca veriye ve saniye başına yüz milyonlarca isteğe ulaşabilir.
* Global olarak dağıtılmıştır. Mobil uygulama kullanıcıları sıklıkla tüm dünya çapında sürekli hareket halindedir. Azure Cosmos DB [global olarak dağıtılmış bir veritabanıdır](distribute-data-globally.md). Verilerinizi kullanıcılarınız tarafından erişilebilir hale getirmek için haritaya tıklayın.
* Yerleşik zengin yetkilendirme. Azure Cosmos DB ile karmaşık özel yetkilendirme kodu olmadan [kullanıcı başına veri](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems) veya çok kullanıcılı paylaşılan veriler gibi popüler düzenleri kolayca uygulayabilirsiniz.
* Jeo-uzamsal sorgular. Birçok mobil uygulama bugün coğrafi bağlamsal deneyimler sunar. [Jeo-uzamsal türler](./sql-query-geospatial-intro.md) için birinci sınıf destekle Azure Cosmos DB bu deneyimleri kolayca oluşturmanızı sağlar.
* İkili dosya ekleri. Uygulama verileriniz genellikle ikili bloblar içerir. Ekler için yerel destek, Azure Cosmos DB’yi uygulama verileriniz için ihtiyacınız olan her şeyi sağlayacak hale getirir.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB ve Xamarin öğreticisi
Aşağıdaki öğreticide Xamarin ve Azure Cosmos DB kullanarak bir mobil uygulamanın nasıl oluşturulacağı gösterilmiştir. Öğreticinin tam kaynak kodunu [GitHub'da Xamarin ve Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) altında bulabilirsiniz.

### <a name="get-started"></a>başlarken
Azure Cosmos DB ile çalışmaya başlamak kolaydır. Azure portala gidin ve yeni bir Azure Cosmos DB hesabı oluşturun. **Hızlı başlangıç** sekmesine tıklayın. Azure Cosmos DB hesabınıza zaten bağlı olan Xamarin Forms yapılacaklar listesi örneğini indirin. 

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-quickstart.png" alt-text="Mobil uygulamalar için Azure Cosmos DB hızlı başlangıcı":::

Bir Xamarin uygulamanız zaten varsa [Azure Cosmos DB NuGet paketini](sql-api-sdk-dotnet-core.md) ekleyebilirsiniz. Azure Cosmos DB, Xamarin. iOS, Xamarin. Android ve Xamarin Forms paylaşılan kitaplıklarını destekler.

### <a name="work-with-data"></a>Verilerle çalışma
Azure Cosmos DB’de veri kayıtlarınız heterojen koleksiyonlarda şemasız JSON belgeleri olarak depolanır. Aynı koleksiyonunda farklı yapılarda belgeleri depolayabilirsiniz:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Xamarin projelerinizde şemasız veriler üzerinde dil ile tümleşik sorgular kullanabilirsiniz:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Kullanıcı ekle
Çoğu Başlarken örneği gibi, indirdiğiniz Azure Cosmos DB örnek, uygulamanın kodunda birincil anahtar sabit kodlanmış bir anahtar kullanarak hizmet kimliğini doğrular. Bu varsayılan yöntem, yerel öykünücünüz dışında bir yerde çalıştırmak istediğiniz uygulamalar için iyi bir seçenek değildir. Yetkisiz bir kullanıcı birincil anahtarı elde alıyorsa, Azure Cosmos DB hesabınızdaki tüm verilerin güvenliği tehlikeye girebilir. Bunun yerine, uygulamanızın yalnızca oturum açmış kullanıcı kayıtlarına erişmesini istersiniz. Azure Cosmos DB, geliştiricilerin bir koleksiyon, bir bölüm anahtarına göre gruplandırılmış belgeler ya da belirli bir belgede uygulama okuma veya okuma/yazma izni vermesini sağlar. 

Yapılacaklar listesi uygulamasını çok kullanıcılı bir yapılacaklar listesi uygulaması olarak değiştirmek için aşağıdaki adımları izleyin: 

  1. Uygulamanıza Facebook, Active Directory veya başka bir sağlayıcı kullanarak oturum açma yöntemi ekleyin.

  2. Bölüm anahtarı olarak **/userId** kullanarak bir Azure Cosmos DB UserItems koleksiyonu oluşturun. Koleksiyonunuz için bölüm anahtarı belirtilmesi, hızlı sorgular sunmaya devam ederken Azure Cosmos DB’nin uygulama kullanıcılarınızın sayısı arttıkça sonsuz ölçeklendirilmesini sağlar.

  3. Azure Cosmos DB Kaynak Belirteç Aracısı ekleyin. Bu basit Web API’si, kullanıcıların kimliklerini doğrular ve giriş yapmış kullanıcılara kısa süreli belirteçler vererek yalnızca kendi bölümlerindeki belgelere erişim sağlar. Bu örnekte Kaynak Belirteç Aracısı, App Service içinde barındırılır.

  4. Uygulamayı Kaynak Belirteç Aracısının Facebook ile kimlik doğrulama yapacağı ve oturum açmış Facebook kullanıcıları için kaynak belirteçleri isteyeceği şekilde değiştirin. Ardından UserItems koleksiyonundaki verilere erişebilirsiniz.  

Bu öğreticinin tam kod örneğini [GitHub'da Kaynak Belirteç Aracısı](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems)’nda bulabilirsiniz. Bu diyagramda çözüm gösterilir:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png" alt-text="Azure Cosmos DB kullanıcılar ve izinler aracısı" border="false":::

İki kullanıcının sahip olmasını istiyorsanız, aynı yapılacaklar listesine erişin, kaynak belirteci aracıdaki erişim belirtecine ek izinler ekleyebilirsiniz.

### <a name="scale-on-demand"></a>İsteğe bağlı olarak ölçeklendirme
Azure Cosmos DB yönetilen bir hizmet olarak veritabanıdır. Kullanıcı sayınız arttığında VM sağlama veya çekirdek sayısını artırma konularında endişelenmeniz gerekmez. Azure Cosmos DB’ye tüm bildirmeniz gereken, uygulamanızın saniyede kaç işlem sayısına ihtiyacı olduğudur. İşlem sayısını **Ölçek** sekmesinde saniye başına istek birimleri (RUs) adlı bir ölçü kullanarak belirtebilirsiniz. Örneğin, 1 KB 'lık bir belgedeki okuma işlemi bir RU gerektirir. Ayrıca trafik büyümesini izlemek ve uyarılar tetiklendiğinde işlem sayısını programlı olarak değiştirmek için **İşlem sayısı** ölçümüne uyarılar ekleyebilirsiniz.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png" alt-text="Azure Cosmos DB talebe göre işlem sayısını ölçeklendirir":::

### <a name="go-planet-scale"></a>Dünya çapında ölçeğe geçin
Uygulamanızın popülerliği arttıkça dünya çapında kullanıcılarınız olabilir. Ayrıca beklenmedik olaylar için hazır olmak isteyebilirsiniz. Azure portala gidin ve Azure Cosmos DB hesabınızı açın. Verilerinizin dünya genelinde istediğiniz sayıda bölgeye çoğaltılması için haritaya tıklayın. Bu özellik, kullanıcılarınız nerede olursa olsun verilerinizi kullanılabilir hale getirir. Ayrıca beklenmedik durumlara karşı hazırlıklı olmak için yük devretme ilkeleri de ekleyebilirsiniz.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png" alt-text="Azure Cosmos DB coğrafi bölgelerde ölçeklenir" border="false":::

Tebrikler. Çözümü tamamladınız, artık Xamarin ve Azure Cosmos DB kullanan bir mobil uygulamanız var. Azure Cosmos DB JavaScript SDK'si kullanarak Cordova uygulamaları ve Azure Cosmos DB REST API'lerini kullanarak yerel iOS/Android uygulamaları geliştirmek için benzer adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [GitHub'da Xamarin ve Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) için kaynak kodu görüntüleyin.
* [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md)’sini indirin.
* [.NET uygulamaları](sql-api-dotnet-samples.md) için daha fazla kod örneği bulun.
* [Azure Cosmos DB’nin zengin sorgu özellikleri](./sql-query-getting-started.md) hakkında bilgi edinin.
* [Azure Cosmos DB’de jeo-uzamsal destek](./sql-query-geospatial-intro.md) hakkında bilgi edinin.
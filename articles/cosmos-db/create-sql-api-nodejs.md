---
title: Hızlı başlangıç-Azure Cosmos DB SQL API hesabından sorgulamak için Node.js kullanma
description: Node.js kullanarak Azure Cosmos DB SQL API hesabına bağlanan ve verileri sorgulayan bir uygulama oluşturma.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: ead4004813cf6415dfa1c7da3d308d93ea49a773
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365919"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Hızlı başlangıç: Azure Cosmos DB SQL API hesabından bağlanmak ve veri sorgulamak için Node.js kullanma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK’sı v4](create-sql-api-java.md)
> * [Spring Verileri v3](create-sql-api-spring-data.md)
> * [Spark v3 Bağlayıcısı](create-sql-api-spark.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

Bu hızlı başlangıçta, Azure portal Azure Cosmos DB bir SQL API hesabı oluşturup, GitHub 'dan kopyalanmış bir Node.js uygulamasını kullanarak yönetirsiniz. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

## <a name="walkthrough-video"></a>İzlenecek yol videosu

Bu makaledeki içeriğe ilişkin kapsamlı bir anlatım için bu videoyu izleyin.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) . [Azure Cosmos DB öykünücüsünü](https://aka.ms/cosmosdb-emulator) bir URI ve anahtar ile de kullanabilirsiniz `https://localhost:8081` `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos hesabı oluşturma

Bu hızlı başlangıç amacıyla Azure Cosmos hesabı oluşturmak için [Azure Cosmos DB dene seçeneğini ücretsiz](https://azure.microsoft.com/try/cosmosdb/) olarak kullanabilirsiniz.

1. [Ücretsiz deneme Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sayfasına gidin.

1. **SQL** API hesabı ' nı seçin ve **Oluştur**' u seçin. Microsoft hesabı kullanarak oturum açın.

1. Oturum açma başarılı olduktan sonra Azure Cosmos hesabınız hazırlanmalıdır. Yeni oluşturulan hesabı açmak için **Azure Portal aç '** ı seçin.

"Ücretsiz Azure Cosmos DB dene" seçeneği bir Azure aboneliği gerektirmez ve 30 günlük sınırlı bir süre boyunca size bir Azure Cosmos hesabı sağlar. Azure Cosmos hesabını daha uzun bir süre için kullanmak istiyorsanız, hesabı Azure aboneliğinizde [oluşturmanız](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) gerekir.

## <a name="add-a-container"></a>Kapsayıcı ekleme

Artık bir veritabanı ve kapsayıcı oluşturmak için Azure portal Veri Gezgini aracı 'nı kullanabilirsiniz.

1. Yeni **Veri Gezgini**  >  **kapsayıcı** seçin.

   **Kapsayıcı Ekle** alanı en sağda görüntülenir, görmek için sağa kaydırmanız gerekebilir.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Azure portalındaki Veri Gezgini, Kapsayıcı Ekle bölmesi":::

2. **Kapsayıcı Ekle** sayfasında, yeni kapsayıcının ayarlarını girin.

   | Ayar           | Önerilen değer | Açıklama                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Veritabanı Kimliği**   | Görevler           | Yeni veritabanınızın adı olarak _Görevler_ girin. Veritabanı adları 1 ila 255 karakterden oluşmalıdır ve `/, \\, #, ?` boşluk içeremez veya sonunda boşluk olamaz. Veritabanı **Işleme sağlama** seçeneğini kontrol edin, veritabanı içindeki tüm kapsayıcılar üzerinde veritabanı için sağlanan aktarım hızını paylaşmanıza olanak sağlar. Bu seçenek maliyet tasarruflarıyla de yardımcı olur. |
   | **İşleme hızı**    | 400             | Aktarım hızını saniyede 400 istek birimi (RU/s) olarak bırakın. Daha sonra gecikme süresini azaltmak isterseniz işlem hızının ölçeğini artırabilirsiniz.                                                                                                                                                                                                                                                    |
   | **Kapsayıcı Kimliği**  | Öğeler           | _Öğeleri_ yeni kapsayıcının adı olarak girin. Kapsayıcı kimliklerinin karakter gereksinimleri, veritabanı adlarına ilişkin karakter gereksinimleri ile aynıdır.                                                                                                                                                                                                                                                               |
   | **Bölüm anahtarı** | /kategori       | Bu makalede açıklanan örnek, bölüm anahtarı olarak _/category_ kullanır.                                                                                                                                                                                                                                                                                                           |

   Önceki ayarlara ek olarak, kapsayıcı için isteğe bağlı olarak **benzersiz anahtarlar** ekleyebilirsiniz. Bu örnekte bu alanı boş bırakalım. Benzersiz anahtarlar sayesinde geliştiriciler veritabanına bir veri bütünlüğü katmanı ekleyebilir. Bir kapsayıcı oluştururken benzersiz bir anahtar ilkesi oluşturarak, bölüm anahtarı başına bir veya daha fazla değerin benzersizliği olduğundan emin olursunuz. Daha fazla bilgi edinmek için [Azure Cosmos DB'de benzersiz anahtarlar](unique-keys.md) makalesine bakın.

   **Tamam**’ı seçin. Veri Gezgini, yeni veritabanını ve kapsayıcıyı görüntüler.

## <a name="add-sample-data"></a>Örnek verileri ekleme

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Verilerinizi sorgulayın

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi GitHub 'dan bir Node.js uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım.

1. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Azure Cosmos veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmeye ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümüne atlayabilirsiniz.

SQL JavaScript SDK 'sının önceki sürümüne alışkın değilseniz _, hüküm ve_ _belge_ koşullarını görmek için kullanabilirsiniz. Azure Cosmos DB [birden çok API modelini](introduction.md)desteklediğinden, [JavaScript SDK 'sının 2.0 + sürümü](https://www.npmjs.com/package/@azure/cosmos) bir koleksiyon, grafik veya tablo olabilecek genel terimler _kapsayıcısını_ ve kapsayıcının içeriğini açıklayan _öğeyi_ kullanır.

Cosmos DB JavaScript SDK 'Sı "" olarak adlandırılır @azure/cosmos ve NPM 'den yüklenebilir...

```bash
npm install @azure/cosmos
```

Aşağıdaki kod parçacıklarının tamamı, _app.js_ dosyasından alınmıştır.

- , `CosmosClient` `@azure/cosmos` NPM paketinden içeri aktarılır.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Yeni bir `CosmosClient` nesne başlatılır.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- "Görevler" veritabanını seçin.

  ```javascript
  const database = client.database(databaseId);
  ```

- "Öğeler" kapsayıcısını/toplamayı seçin.

  ```javascript
  const container = database.container(containerId);
  ```

- "Öğeler" kapsayıcısındaki tüm öğeleri seçin.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Yeni bir öğe oluşturun

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Öğeyi güncelleştirme

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Öğeyi silme

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> Hem "güncelleştirme" hem de "silme" yöntemlerinde, öğesi çağırarak veritabanından seçilmelidir `container.item()` . Geçirilen iki parametre öğenin kimliği ve öğenin bölüm anahtarıdır. Bu durumda, Parma anahtarı "Category" alanının değeridir.

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Şimdi Azure Cosmos hesabınızın bağlantı dizesi ayrıntılarını almak için Azure portal geri dönün. Veritabanınıza bağlanabilmeleri için bağlantı dizesini uygulamaya kopyalayın.

1. [Azure portal](https://portal.azure.com/)Azure Cosmos DB hesabınızda, sol gezinti bölmesinde **anahtarlar** ' ı seçin ve ardından **okuma-yazma anahtarları**' nı seçin. Bir sonraki adımda _app.js_ dosyasına URI ve birincil anahtar kopyalamak için ekranın sağ tarafındaki kopyalama düğmelerini kullanın.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Azure portalında erişim anahtarı görüntüleme ve kopyalama, Anahtarlar dikey penceresi":::

2. İçinde _config.js_ dosyasını açın.

3. Portaldan URI değerini kopyalayın (kopyalama düğmesini kullanarak) ve _config.js_ uç nokta anahtarının değeri yapın.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Ardından, portaldan BIRINCIL anahtar değerini kopyalayın ve `config.key` _config.js_ değeri yapın. Bu adımlarla uygulamanıza Azure Cosmos DB ile iletişim kurması için gereken tüm bilgileri eklemiş oldunuz.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. `npm install`" @azure/cosmos " NPM paketini yüklemek için bir terminalde Çalıştır

2. Node.js uygulamanızı başlatmak için bir terminalde `node app.js` komutunu çalıştırın.

3. Bu hızlı başlangıçta daha önce oluşturduğunuz iki öğe listelenmiştir. Yeni bir öğe oluşturulur. Bu öğenin "ıstamamlanmıştır" bayrağı "true" olarak güncelleştirildiğinden öğe silinir.

Bu örnek uygulamayla denemeler yapmaya devam edebilir veya Veri Gezgini, değiştirebilir ve verilerle çalışabilirsiniz.

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure Cosmos DB hesabı oluşturmayı, Veri Gezgini kullanarak bir kapsayıcı oluşturmayı ve Node.js uygulamayı çalıştırmayı öğrendiniz. Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB 'ye veri aktarma](import-data.md)

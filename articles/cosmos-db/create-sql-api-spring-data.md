---
title: Hızlı başlangıç-Azure Cosmos DB kullanarak bir belge veritabanı oluşturmak için Azure Cosmos DB v3 verilerini kullanın
description: Bu hızlı başlangıç, Azure Cosmos DB SQL API 'sine bağlanmak ve sorgu sorgulamak için kullanabileceğiniz bir Spring Data Azure Cosmos DB v3 kodu örneği sunar
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a094ebb6b218027c195985312b023ccb8838f703
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365695"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Hızlı başlangıç: Azure Cosmos DB SQL API verilerini yönetmek için v3 uygulaması Azure Cosmos DB bir Spring Data oluşturun
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK’sı v4](create-sql-api-java.md)
> * [Spring Verileri v3](create-sql-api-spring-data.md)
> * [Spark v3 Bağlayıcısı](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Bu hızlı başlangıçta, Azure portal Azure Cosmos DB bir SQL API hesabı oluşturup yönettiğinizde, GitHub 'dan kopyalanmış bir Spring Data Azure Cosmos DB v3 uygulaması kullanarak. İlk olarak, Azure portal kullanarak bir Azure Cosmos DB SQL API hesabı oluşturun ve ardından Spring Data Azure Cosmos DB v3 bağlayıcısını kullanarak bir Spring Boot uygulaması oluşturun ve ardından Spring Boot uygulamasını kullanarak Cosmos DB hesabınıza kaynak eklersiniz. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

> [!IMPORTANT]  
> Bu sürüm notları, Spring Data Azure Cosmos DB sürüm 3 ' ü yöneliktir. [Sürüm 2 için sürüm notlarını buradan](sql-api-sdk-java-spring-v2.md)bulabilirsiniz. 
>
> Spring Data Azure Cosmos DB yalnızca SQL API 'sini destekler.
>
> Diğer Azure Cosmos DB API 'Lerinde yay verileri hakkında bilgi için şu makalelere bakın:
> * [Azure Cosmos DB ile Apache Cassandra için yay verileri](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB ile Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Yay verileri Gremlin Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) . [Azure Cosmos DB öykünücüsünü](https://aka.ms/cosmosdb-emulator) bir URI ve anahtar ile de kullanabilirsiniz `https://localhost:8081` `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME`Ortam değişkeninizi JDK 'nin yüklü olduğu klasöre işaret edin.
- [Maven ikili Arşivi](https://maven.apache.org/download.cgi). Ubuntu 'da, `apt-get install maven` Maven 'yi yüklemek için komutunu çalıştırın.
- [Git](https://www.git-scm.com/downloads). Ubuntu 'da git 'i `sudo apt-get install git` yüklemek için komutunu çalıştırın.

## <a name="introductory-notes"></a>Giriş notları

*Cosmos DB hesabının yapısı.* API veya programlama dilinden bağımsız olarak, bir Cosmos DB *hesabı* sıfır veya daha fazla *veritabanı* içerir, bir *veritabanı* (DB) sıfır veya daha fazla *kapsayıcı* içerir ve bir *kapsayıcı* , aşağıdaki diyagramda gösterildiği gibi sıfır veya daha fazla öğe içerir:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos hesabı varlıkları" border="false":::

Burada veritabanları, kapsayıcılar ve öğeler hakkında daha fazla bilgi edinebilirsiniz [.](account-databases-containers-items.md) Birkaç önemli özellik, kapsayıcı düzeyinde, *sağlanan aktarım hızı* ve *bölüm anahtarı* arasında tanımlanır. 

Sağlanan aktarım hızı, parasal bir fiyata sahip olan Istek *birimleri (ru*) cinsinden ölçülür ve hesabın işletim maliyetinde faktörü önemli ölçüde belirler. Sağlanan aktarım hızı kapsayıcı başına ayrıntı düzeyi veya veritabanı başına ayrıntı düzeyinde seçilebilir, ancak kapsayıcı düzeyinde üretilen iş belirtimi genellikle tercih edilir. Burada üretilen iş sağlama hakkında daha fazla bilgi alabilirsiniz [.](set-throughput.md)

Öğeler bir Cosmos DB kapsayıcısına eklendikçe, istekleri işlemek için daha fazla depolama ve işlem eklenerek veritabanı yatay olarak artar. Depolama ve işlem kapasitesi, *bölümler* olarak bilinen ayrı birimlere eklenir ve belgelerinizdeki bir alanı her belgeyi bir bölüme eşleyen bölüm anahtarı olacak şekilde seçmeniz gerekir. Bölümlerin yönetilme şekli, her bölüme bölüm anahtarı değerlerinin aralığından kabaca eşit bir dilim atanabileceği; Bu nedenle, görece rastgele veya eşit olarak dağıtılan bir bölüm anahtarı seçmeniz önerilir. Aksi halde, bazı bölümler önemli ölçüde daha az istek *(**soğuk bölüm*) görür ve bu da kaçınılmaz. [Burada](partitioning-overview.md)bölümlendirme hakkında daha fazla bilgi edinebilirsiniz.

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

Bir belge veritabanı oluşturmadan önce Azure Cosmos DB ile bir SQL API hesabı oluşturmanız gerekir.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Kapsayıcı ekleme

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Örnek verileri ekleme

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Verilerinizi sorgulayın

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi kod ile çalışmaya geçelim. GitHub'dan bir SQL API'si uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz. 

Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi takdirde, [Uygulamayı çalıştırma](#run-the-app) konusuna atlayabilirsiniz. 

### <a name="application-configuration-file"></a>Uygulama yapılandırma dosyası

Burada Spring Boot ve Spring verilerinin Kullanıcı deneyimini nasıl geliştireceğiz-Cosmos istemcisi oluşturma ve Cosmos kaynaklarına bağlanma işlemi kod yerine artık config olur. Uygulama başlangıç Ilkbaharında önyükleme, **uygulama. Özellikler**' deki ayarları kullanarak bu ortak sürümü gerçekleştirir:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Bir Azure Cosmos DB hesabı, veritabanı ve kapsayıcı oluşturduktan sonra, yapılandırma dosyasında yalnızca boş olanları doldurmanız ve Spring Boot/Spring verileri otomatik olarak şunları yapacaktır: (1) URI ve anahtarla temel bir Java SDK `CosmosClient` örneği oluşturun ve (2) veritabanına ve kapsayıcıya bağlanın. Artık hazırsınız. **daha fazla kaynak yönetimi kodu yok!**

### <a name="java-source"></a>Java kaynağı

Spring Data değeri-Add, veri depolarında çalışan basit, temiz, standartlaştırılmış ve platformdan bağımsız arabiriminden de gelir. Yukarıdaki Spring Data GitHub örneği üzerinde oluşturma, aşağıda yer alan ve Spring Data Azure Cosmos DB ile Azure Cosmos DB belgelerin işlenmesine yönelik sorgu örnekleri verilmiştir.

* Yöntemini kullanarak öğe oluşturma ve güncelleştirme `save` .

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Nokta-deposunda tanımlanan türetilmiş sorgu yöntemini kullanarak okur. , `findByIdAndLastName` İçin noktası için okuma yapar `UserRepository` . Yöntem adı bölümünde bahsedilen alanlar, Spring verilerinin ve alanları tarafından tanımlanan bir nokta okumayı yürütmesine neden olur `id` `lastName` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Öğe şunu kullanarak siler `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Depo yöntemi adına göre türetilmiş sorgu. Spring Data, `UserRepository` `findByFirstName` yöntemi alan üzerinde BIR Java SDK SQL sorgusu olarak uygular `firstName` (Bu sorgu bir nokta okuma olarak uygulanamadı):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Bu adımda Azure portala dönerek bağlantı dizesi bilgilerinizi alın ve uç nokta bilgileriyle uygulamayı çalıştırın. Bu, uygulamanızın barındırılan veritabanıyla iletişim kurmasına olanak tanır.

1. git terminal penceresinde `cd` komutuyla örnek kod klasörüne gidin.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. Git Terminal penceresinde, gerekli Spring Data Azure Cosmos DB paketlerini yüklemek için aşağıdaki komutu kullanın.

    ```bash
    mvn clean package
    ```

3. Git Terminal penceresinde, Spring Data Azure Cosmos DB uygulamasını başlatmak için aşağıdaki komutu kullanın:

    ```bash
    mvn spring-boot:run
    ```
    
4. Uygulama App **. Properties** yükler ve Azure Cosmos DB hesabınızdaki kaynakları bağlar.
5. Uygulama yukarıda açıklanan nokta CRUD işlemlerini gerçekleştirir.
6. Uygulama, türetilmiş bir sorgu gerçekleştirecek.
7. Uygulama kaynaklarınızı silmez. Ücret ödemeden kaçınmak isterseniz, hesabınızdaki [kaynakları temizlemek](#clean-up-resources) için portala geri dönün.

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Cosmos DB bir SQL API hesabı oluşturmayı, Veri Gezgini kullanarak bir belge veritabanı ve kapsayıcı oluşturmayı ve aynı şeyi programlı bir şekilde yapmak için bir Spring Data uygulaması çalıştırmayı öğrendiniz. Artık Azure Cosmos DB hesabınıza daha fazla veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB hesabınıza veri aktarma](import-data.md)
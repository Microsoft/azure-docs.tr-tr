---
title: "NoSQL öğreticisi: Azure Cosmos DB Java SDK 'Sı için SQL API"
description: Azure Cosmos DB için SQL API’sini kullanarak çevrimiçi bir veritabanı ve Java konsol uygulaması oluşturan bir NoSQL öğreticisi. Azure SQL, JSON için bir NoSQL veritabanıdır.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: a046f97dccdcc4a9cb9fe180447c1ff9a316f0df
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720806"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>NoSQL öğreticisi: SQL API Java konsol uygulaması derleme

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB Java SDK’sı için SQL API’sine yönelik NoSQL öğreticisine hoş geldiniz! Bu öğreticiyi uyguladıktan sonra, Azure Cosmos DB kaynaklarını oluşturan ve sorgulayan bir konsol uygulamasına sahip olacaksınız.

Kapsanan konular:

* Azure Cosmos DB hesabı oluşturma ve hesaba bağlanma
* Visual Studio Çözümünüzü yapılandırma
* Çevrimiçi bir veritabanı oluşturma
* Koleksiyon oluşturma
* JSON belgeleri oluşturma
* Koleksiyonu sorgulama
* JSON belgeleri oluşturma
* Koleksiyonu sorgulama
* Bir belgeyi değiştirme
* Bir belgeyi silme
* Veritabanını silme

Şimdi başlayalım!

## <a name="prerequisites"></a>Ön koşullar
Aşağıdakilere sahip olduğunuzdan emin olun:

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) için kaydolabilirsiniz. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java Geliştirme Seti (JDK) 7 +](https://aka.ms/azure-jdks).
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1\. Adım: Azure Cosmos DB hesabı oluşturma
Bir Azure Cosmos DB hesabı oluşturalım. Kullanmak istediğiniz bir hesap zaten varsa [GitHub projesini kopyalama](#GitClone) adımına atlayabilirsiniz. Azure Cosmos DB Öykünücüsü’nü kullanıyorsanız öykünücünün kurulumunu gerçekleştirmek için [Azure Cosmos DB Öykünücüsü](local-emulator.md) konusundaki adımları izleyin ve [GitHub projesini kopyalama](#GitClone) adımına atlayın.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>2. Adım: GitHub projesini kopyalama
[Azure Cosmos DB ve Java’yı kullanmaya başlama](https://github.com/Azure-Samples/documentdb-java-getting-started) GitHub deposunu kopyalayarak başlayabilirsiniz. Örneğin, yerel bir dizinden örnek projesini yerele almak için aşağıdaki komutu çalıştırın.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Dizinde proje için bir `pom.xml` nesnesinin yanı sıra Java kaynak kodunu içeren `src` klasörü vardır. Bu klasör içindeki `Program.java`, Azure Cosmos DB ile belge oluşturma ve bir koleksiyondaki verileri sorgulama gibi basit işlemlerin nasıl yapılacağını gösterir. `pom.xml`, [Maven üzerindeki Azure Cosmos DB Java SDK’sı](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) bağımlılığını içerir.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>3. Adım: Azure Cosmos DB hesabına bağlanma
Ardından, uç noktanızı ve birincil ana anahtarınızı almak için tekrar [Azure portala](https://portal.azure.com) gidin. Azure Cosmos DB uç noktası ve birincil anahtar, uygulamanızın nereye bağlanacağını anlaması ve Azure Cosmos DB’nin uygulamanızın bağlantısına güvenmesi için gereklidir.

Azure portalında Azure Cosmos DB hesabınıza gidin ve ardından **Anahtarlar**’a tıklayın. Portaldaki URI’yi kopyalayın ve Program.java dosyasındaki `https://FILLME.documents.azure.com` içine yapıştırın. Ardından portaldan BİRİNCİL ANAHTARI kopyalayın ve `FILLME` içine yapıştırın.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Bir Java konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan Azure portal ekran görüntüsü. Azure Cosmos DB hesabı dikey penceresinde ANAHTARLAR düğmesi vurgulanmış, ETKİN hub'ı vurgulanmış ve Anahtarlar dikey penceresinde URI, BİRİNCİL ANAHTAR ve İKİNCİL ANAHTAR değerleri vurgulanmış bir Azure Cosmos DB hesabını gösterir][keys]

## <a name="step-4-create-a-database"></a>4\. Adım: Veritabanı oluşturma
Azure Cosmos DB [veritabanınız](databases-containers-items.md#azure-cosmos-databases), [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) sınıfının **createDatabase** yöntemi kullanılarak oluşturulabilir. Veritabanı, koleksiyonlar genelinde bölümlenmiş JSON belgesi depolama alanının mantıksal bir kapsayıcısıdır.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>5. Adım: Koleksiyon oluşturma
> [!WARNING]
> **createCollection**, ayrılmış işleme ile yeni bir koleksiyon oluşturur, bu da ücret ödenmesini gerektirebilir. Daha ayrıntılı bilgi için [fiyatlandırma sayfamızı](https://azure.microsoft.com/pricing/details/cosmos-db/) ziyaret edin.
> 
> 

Bir koleksiyon, **Documentclient** sınıfının [CreateCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) yöntemi kullanılarak oluşturulabilir. Koleksiyon, JSON belgelerinin ve ilişkili JavaScript uygulama mantığının bir kapsayıcısıdır.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos containers can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>6. Adım: JSON belgeleri oluşturma
Belge, **Documentclient** sınıfının [CreateDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) yöntemi kullanılarak oluşturulabilir. Belgeler, kullanıcı tanımlı (rastgele) JSON içerikleridir. Şimdi bir veya daha fazla belge ekleyebiliriz. Veritabanınızda depolamak istediğiniz veriler zaten varsa, verileri bir veritabanına içeri aktarmak için Azure Cosmos DB’nin [Veri Geçişi aracını](import-data.md) kullanabilirsiniz.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Bir Java konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan belgeler, hesap, çevrimiçi veritabanı ve koleksiyon arasındaki hiyerarşik ilişkiyi gösteren diyagram](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. Adım: Azure Cosmos DB kaynaklarını sorgulama
Azure Cosmos DB, her bir koleksiyonda depolanan JSON belgeleri için [zengin sorguların](how-to-sql-query.md) gerçekleştirilmesini destekler.  Aşağıdaki örnek kodda Azure Cosmos DB içindeki belgelerin SQL söz dizimi ve [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments) yöntemi kullanılarak nasıl sorgulanacağı gösterilmektedir.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>8. Adım: JSON belgesini değiştirme
Azure Cosmos DB, JSON belgelerinin [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument) yöntemiyle güncelleştirilmesini destekler.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>9. Adım: JSON belgesini silme
Benzer şekilde, Azure Cosmos DB, JSON belgelerinin [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) yöntemiyle silinmesini destekler.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>10. Adım: Veritabanını silme
Oluşturulan veritabanı silindiğinde, veritabanı ve tüm alt kaynaklar (koleksiyonlar, belgeler vb.) kaldırılır.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>11. Adım: Java konsol uygulamanızı hep birlikte çalıştırın!
Konsoldan uygulamayı çalıştırmak için Maven kullanarak proje klasörüne gidin ve derleyin:
    
    mvn package

`mvn package` komutunu çalıştırdığınızda Maven’dan en yeni Azure Cosmos DB kitaplığı indirilir ve `GetStarted-0.0.1-SNAPSHOT.jar` üretilir. Ardından şu komutla uygulamayı çalıştırın:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Tebrikler! Bu NoSQL öğreticisini tamamladınız ve çalışan bir Java konsol uygulamasına sahipsiniz!

## <a name="next-steps"></a>Sonraki adımlar
* Java web uygulaması öğreticisi ister misiniz? Bkz. [Azure Cosmos DB kullanarak Java ile bir web uygulaması oluşturma](sql-api-java-application.md).
* [Azure Cosmos DB hesabını nasıl izleyebileceğinizi](monitor-accounts.md) öğrenin.
* [Query Playground](https://www.documentdb.com/sql/demo)'daki örnek veri kümelerimizde sorgular çalıştırın.

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png

---
title: "NoSQL öğreticisi: Azure DocumentDB Java SDK’sı | Microsoft Docs"
description: "DocumentDB Java SDK’sını kullanarak çevrimiçi bir veritabanı ve Java konsol uygulaması oluşturan bir NoSQL öğreticisi. Azure DocumentDB, JSON için bir NoSQL veritabanıdır."
keywords: "nosql öğreticisi, çevrimiçi veritabanı, java konsol uygulaması"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>NoSQL öğreticisi: DocumentDB Java konsol uygulaması oluşturma
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB için Node.js](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure DocumentDB Java SDK’sı için NoSQL öğreticisine hoş geldiniz! Bu öğreticiyi uyguladıktan sonra, DocumentDB kaynaklarını oluşturan ve sorgulayan bir konsol uygulamasına sahip olacaksınız.

Kapsanan konular:

* DocumentDB hesabı oluşturma ve DocumentDB hesabına bağlanma
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

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) için kaydolabilirsiniz. Alternatif olarak bu öğretici için [Azure DocumentDB Öykünücüsü](documentdb-nosql-local-emulator.md)’nü kullanabilirsiniz.
* [Git](https://git-scm.com/downloads)
* [Java Geliştirme Seti (JDK) 7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>1. Adım: DocumentDB hesabı oluşturma
Bir DocumentDB hesabı oluşturalım. Kullanmak istediğiniz bir hesap zaten varsa [GitHub projesini kopyalama](#GitClone) adımına atlayabilirsiniz. DocumentDB Öykünücüsü’nü kullanıyorsanız öykünücünün kurulumunu gerçekleştirmek için [Azure DocumentDB Öykünücüsü](documentdb-nosql-local-emulator.md) konusundaki adımları izleyin ve [GitHub projesini kopyalama](#GitClone) adımına atlayın.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>2. Adım: GitHub projesini kopyalama
[DocumentDB ve Java’yı kullanmaya başlama](https://github.com/Azure-Samples/documentdb-java-getting-started) GitHub deposunu kopyalayarak başlayabilirsiniz. Örneğin, yerel bir dizinden örnek projesini yerele almak için aşağıdaki komutu çalıştırın.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

Dizinde proje için bir `pom.xml` nesnesinin yanı sıra Java kaynak kodunu içeren `src` klasörü vardır. Bu klasör içindeki `Program.java`, Azure DocumentDB ile belge oluşturma ve bir koleksiyondaki verileri sorgulama gibi basit işlemlerin nasıl yapılacağını gösterir. `pom.xml`, [Maven üzerindeki DocumentDB Java SDK’sı](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) bağımlılığı içerir.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>3. Adım: DocumentDB hesabına bağlanma
Ardından, uç noktanızı ve birincil ana anahtarınızı almak için tekrar [Azure Portal](https://portal.azure.com)’a gidin. DocumentDB uç noktası ve birincil anahtar, uygulamanızın nereye bağlanacağını anlaması ve DocumentDB’nin uygulamanızın bağlantısına güvenmesi için gereklidir.

Azure Portal'da DocumentDB hesabınıza gidin ve ardından **Anahtarlar**’a tıklayın. Portaldaki URI’yi kopyalayın ve Program.java dosyasındaki `<your endpoint URI>` içine yapıştırın. Ardından portaldan BİRİNCİL ANAHTARI kopyalayın ve `<your key>` içine yapıştırın.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Bir Java konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan Azure Portal’ın ekran görüntüsü. DocumentDB hesabı dikey penceresinde ANAHTARLAR düğmesi vurgulanmış, ETKİN hub'ı vurgulanmış ve Anahtarlar dikey penceresinde URI, BİRİNCİL ANAHTAR ve İKİNCİL ANAHTAR değerleri vurgulanmış bir DocumentDB hesabını gösterir][keys]

## <a name="step-4-create-a-database"></a>4. Adım: Veritabanı oluşturma
DocumentDB [veritabanınız](documentdb-resources.md#databases), **DocumentClient** sınıfının [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) yöntemi kullanılarak oluşturulabilir. Veritabanı, koleksiyonlar genelinde bölümlenmiş JSON belgesi depolama alanının mantıksal bir kapsayıcısıdır.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>5. Adım: Koleksiyon oluşturma
> [!WARNING]
> **createCollection**, ayrılmış işleme ile yeni bir koleksiyon oluşturur, bu da ücret ödenmesini gerektirebilir. Daha ayrıntılı bilgi için [fiyatlandırma sayfamızı](https://azure.microsoft.com/pricing/details/documentdb/) ziyaret edin.
> 
> 

Bir [koleksiyon](documentdb-resources.md#collections), **DocumentClient** sınıfının [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) metodu kullanılarak oluşturulabilir. Koleksiyon, JSON belgelerinin ve ilişkili JavaScript uygulama mantığının bir kapsayıcısıdır.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>6. Adım: JSON belgeleri oluşturma
Bir [belge](documentdb-resources.md#documents), **DocumentClient** sınıfının [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) metodu kullanılarak oluşturulabilir. Belgeler, kullanıcı tanımlı (rastgele) JSON içerikleridir. Şimdi bir veya daha fazla belge ekleyebiliriz. Veritabanınızda depolamak istediğiniz veriler zaten varsa, verileri veritabanına aktarmak için DocumentDB'nin [Veri Geçiş Aracı](documentdb-import-data.md)'nı kullanabilirsiniz.

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

![Bir Java konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan belgeler, hesap, çevrimiçi veritabanı ve koleksiyon arasındaki hiyerarşik ilişkiyi gösteren diyagram](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. Adım: DocumentDB kaynaklarını sorgulama
DocumentDB, her bir koleksiyonda depolanan JSON belgelerde yapılan zengin [sorguları](documentdb-sql-query.md) destekler.  Aşağıdaki örnek kodda DocumentDB içindeki belgelerin SQL söz dizimi ve [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-) metodu kullanılarak nasıl sorgulanacağı gösterilmektedir.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>8. Adım: JSON belgesini değiştirme
DocumentDB, [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-) metoduyla JSON belgelerinin güncelleştirilmesini destekler.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>9. Adım: JSON belgesini silme
Benzer şekilde DocumentDB, [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-) metoduyla JSON belgelerinin silinmesini de destekler.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>10. Adım: Veritabanını silme
Oluşturulan veritabanı silindiğinde, veritabanı ve tüm alt kaynaklar (koleksiyonlar, belgeler vb.) kaldırılır.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>11. Adım: Java konsol uygulamanızı hep birlikte çalıştırın!
Uygulamayı konsoldan çalıştırmak için önce Maven kullanarak derleyin:
    
    mvn package

`mvn package` komutunu çalıştırdığınızda Maven’dan en yeni DocumentDB kitaplığı indirilir ve `GetStarted-0.0.1-SNAPSHOT.jar` üretilir. Ardından şu komutla uygulamayı çalıştırın:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Tebrikler! Bu NoSQL öğreticisini tamamladınız ve çalışan bir Java konsol uygulamasına sahipsiniz!

## <a name="next-steps"></a>Sonraki adımlar
* Java web uygulaması öğreticisi ister misiniz? Bkz. [DocumentDB kullanarak Java ile bir web uygulaması oluşturma](documentdb-java-application.md).
* [Bir DocumentDB hesabını izleme](documentdb-monitor-accounts.md) hakkında bilgi edinin.
* [Query Playground](https://www.documentdb.com/sql/demo)'daki örnek veri kümelerimizde sorgular çalıştırın.
* [DocumentDB belge sayfasının](https://azure.microsoft.com/documentation/services/documentdb/) Geliştirme bölümünde programlama modeli hakkında daha fazla bilgi edinin.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png


---
title: "Hızlı başlangıç: Mongo DB ve Java SDK için Azure Cosmos DB API 'sini kullanarak bir Web uygulaması oluşturma"
description: MongoDB için Azure Cosmos DB API 'sini kullanarak bağlanmak ve sorgulamak için kullanabileceğiniz bir Java kod örneği oluşturmayı öğrenin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f91cf5103fa8bf324fe372d2f12736a426fe8aed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101656542"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Hızlı başlangıç: Azure Cosmos DB 'de Java ve MongoDB API 'SI ile bir konsol uygulaması oluşturma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Bu hızlı başlangıçta, Azure portal bir MongoDB API hesabı için Azure Cosmos DB oluşturup yönetirsiniz ve GitHub ' dan kopyalanmış bir Java SDK uygulamasını kullanarak veri eklersiniz. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

## <a name="prerequisites"></a>Önkoşullar
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) . [Azure Cosmos DB öykünücüsünü](https://aka.ms/cosmosdb-emulator) bağlantı dizesiyle de kullanabilirsiniz `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Java Development Kit (JDK) sürüm 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). 
- [Maven](https://maven.apache.org/download.cgi). Veya `apt-get install maven` Maven 'yi yüklemek için ' i çalıştırın.
- [Git](https://git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Koleksiyon ekleme

Yeni **veritabanı veritabanınızı** ve yeni koleksiyon **kol** değerini adlandırın.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-mongodb-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi GitHub 'dan bir uygulama kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz. 

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Daha sonra kodu sık kullandığınız düzenleyicinizde açın. 

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümüne atlayabilirsiniz. 

Aşağıdaki kod parçacıklarının hepsi *program. Java* dosyasından alınmıştır.

Bu konsol uygulaması [MongoDB Java sürücüsünü](https://docs.mongodb.com/ecosystem/drivers/java/)kullanır. 

* DocumentClient başlatılır.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Yeni bir veritabanı ve koleksiyonu oluşturulur.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* `MongoCollection.insertOne` kullanılarak birkaç belge eklenir

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* `MongoCollection.find` kullanılarak birkaç sorgu gerçekleştirilir

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Bu adımda Azure portalına dönerek bağlantı dizesi bilgilerinizi kopyalayıp uygulamaya ekleyin.

1. Azure Cosmos DB hesabınızda **hızlı başlangıç**' i seçin, **Java**' yı seçin, sonra bağlantı dizesini panonuza kopyalayın.

2. *Program. Java* dosyasını açın, MongoClientURI oluşturucusuna bağımsız değişkenini bağlantı dizesiyle değiştirin. Bu adımlarla uygulamanıza Azure Cosmos DB ile iletişim kurması için gereken tüm bilgileri eklemiş oldunuz. 
    
## <a name="run-the-console-app"></a>Konsol uygulamasını çalıştırma

1. Gerekli npm modüllerini yüklemek için bir terminalde `mvn package` komutunu çalıştırın

2. Java uygulamanızı başlatmak için bir terminalde `mvn exec:java -D exec.mainClass=GetStarted.Program` komutunu çalıştırın.

Artık [](mongodb-robomongo.md)  /  Bu yeni verileri sorgulamak, değiştirmek ve bunlarla çalışmak için robomongo 'yu[Studio 3T](mongodb-mongochef.md) 'i kullanabilirsiniz.

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Mongo DB hesabı için Azure Cosmos DB API oluşturma, Veri Gezgini kullanarak bir veritabanı ve kapsayıcı ekleme ve Java konsol uygulaması kullanarak veri ekleme hakkında öğrendiniz. Artık Cosmos veritabanınıza ek veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
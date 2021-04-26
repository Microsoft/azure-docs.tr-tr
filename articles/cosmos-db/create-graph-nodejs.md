---
title: Gremlin API kullanarak Azure Cosmos DB Node.js uygulaması oluşturma
description: Azure Cosmos DB'ye bağlanmak ve veritabanını sorgulamak için kullanabileceğiniz bir Node.js kod örneği sunar
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 0b88456bd245f79c38d7829dca76de702f768c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360148"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>Hızlı başlangıç: Azure Cosmos DB Gremlin API hesabını kullanarak bir Node.js uygulaması derleme
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin konsolu](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Bu hızlı başlangıçta, Azure portal bir Azure Cosmos DB Gremlin (Graf) API hesabı oluşturup yönetirsiniz ve GitHub ' dan kopyalanmış bir Node.js uygulamasını kullanarak veri eklersiniz. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

## <a name="prerequisites"></a>Önkoşullar
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Node.js 0.10.29 +](https://nodejs.org/).
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Graf ekleme

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi GitHub'dan bir Gremlin API'si uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Çözüm dosyasını Visual Studio'da açın. 

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümüne atlayabilirsiniz. 

Aşağıdaki kod parçacıklarının tamamı, *app.js* dosyasından alınmıştır.

Bu konsol uygulaması açık kaynaklı [Gremlin Node.js](https://www.npmjs.com/package/gremlin) sürücüsünü kullanır.

* Gremlin istemcisi oluşturulur.

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  Yapılandırmaların hepsi *config.js*, [aşağıdaki bölümde](#update-your-connection-string)düzenliyoruz.

* Farklı Gremlin işlemlerini yürütmek için bir dizi işlev tanımlanır. Bu, bunlardan biridir:

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* Her işlev bir Gremlin sorgu dizisi parametresi olan bir `client.execute` yöntemi yürütür. `g.V().count()` öğesinin nasıl yürütüldüğü hakkında bir örnek aşağıdadır:

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* Dosyanın sonunda, tüm yöntemler çağrılır. Bu işlem yöntemleri art arda yürütür:

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

1. *config.js* dosyasını açın. 

2. *config.js*, `config.endpoint` Azure Portal Cosmos DB hesabınızın **genel bakış** sayfasında bulunan **Gremlin uç noktası** değeriyle anahtarı girin. 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    :::image type="content" source="./media/create-graph-nodejs/gremlin-uri.png" alt-text="Azure portal, Genel Bakış sayfasında erişim anahtarını görüntüleme ve kopyalama":::

3. *config.js*, config. PrimaryKey değerini, Azure Portal Cosmos DB hesabınızın **anahtarlar** sayfasından **birincil anahtar** değeriyle birlikte girin. 

    `config.primaryKey = "PRIMARYKEY";`

   :::image type="content" source="./media/create-graph-nodejs/keys.png" alt-text="Azure portal tuşları dikey penceresi":::

4. Veritabanı adını ve config.database ve config.collection değerinin grafik (kapsayıcı) adını girin. 

İşte tamamlanan *config.js* dosyanızın şöyle görünmesi gerekir:

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Konsol uygulamasını çalıştırma

1. Bir Terminal penceresi açın ve `cd` projede yer alan *package.js* dosya için yükleme dizinine (komutunu kullanarak) değiştirin.

2. `gremlin` dahil gerekli npm modüllerini yüklemek için `npm install` öğesini çalıştırın.

3. Node.js uygulamanızı başlatmak için bir terminalde `node app.js` komutunu çalıştırın.

## <a name="browse-with-data-explorer"></a>Veri Gezgini ile Göz Ama

Artık Azure portalındaki Veri Gezgini'ne dönerek yeni grafik verilerinizi görüntüleyebilir, sorgulayabilir, değiştirebilir ve bu verilerle çalışabilirsiniz.

Yeni veritabanı, Veri Gezgini'nin **Graflar** bölmesinde görüntülenir. Veritabanını ve ardından kapsayıcıyı genişletip **Graf**’ı seçin.

Örnek uygulama tarafından oluşturulan veriler, **Filtre Uygula**’yı seçtiğinizde **Graf** sekmesinin sonraki bölmesinde gösterilir.

Filtreyi test etmek için `g.V()` işlemini `.has('firstName', 'Thomas')` ile tamamlamayı deneyin. Değerin büyük küçük harfe duyarlı olduğunu unutmayın.

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Kaynaklarınızı temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Cosmos DB bir hesap oluşturmayı, Veri Gezgini kullanarak bir grafik oluşturmayı ve grafiğe veri eklemek için bir Node.js uygulamasını çalıştırmayı öğrendiniz. Artık daha karmaşık sorgular oluşturabilir ve Gremlin kullanarak güçlü grafik geçişi mantığını kullanabilirsiniz. 

> [!div class="nextstepaction"]
> [Gremlin kullanarak sorgulama](tutorial-query-graph.md)

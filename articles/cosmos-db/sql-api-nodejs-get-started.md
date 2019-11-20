---
title: Azure Cosmos DB için SQL API için Node. js öğreticisi
description: SQL API ile Azure Cosmos DB bağlantısı kurma ve sorgulama yapma adımlarını gösteren bir Node.js öğreticisi
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: a273b89d864d97d9bc71acff476371f77cfff066
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754831"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Öğretici: Azure Cosmos DB SQL API verilerini yönetmek için JavaScript SDK 'Sı ile Node. js konsol uygulaması derleme

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Geliştirici olarak, NoSQL belge verileri kullanan uygulamalarınız olabilir. Bu belge verilerini depolamak ve erişmek için Azure Cosmos DB bir SQL API hesabı kullanabilirsiniz. Bu öğreticide, Azure Cosmos DB kaynakları oluşturmak ve sorgulamak için bir Node. js konsol uygulaması oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Azure Cosmos DB bir hesap oluşturun ve bir hesabı bağlayın.
> * Uygulamanızı ayarlayın.
> * Bir veritabanı oluşturun.
> * Bir kapsayıcı oluşturun.
> * Kapsayıcıya öğe ekleyin.
> * Öğeler, kapsayıcı ve veritabanı üzerinde temel işlemleri gerçekleştirin.

## <a name="prerequisites"></a>Önkoşullar 

Aşağıdaki kaynaklara sahip olduğunuzdan emin olun:

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [Ücretsiz Azure Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node. js](https://nodejs.org/) v 6.0.0 veya üzeri.

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluştur

Bir Azure Cosmos DB hesabı oluşturalım. Kullanmak istediğiniz bir hesap zaten varsa [Node.js uygulamanızı ayarlama](#SetupNode) adımına atlayabilirsiniz. Azure Cosmos DB Öykünücüsü’nü kullanıyorsanız öykünücünün kurulumunu gerçekleştirmek için [Azure Cosmos DB Öykünücüsü](local-emulator.md) konusundaki adımları izleyin ve [Node.js uygulamanızı ayarlama](#SetupNode) adımına atlayın. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Node. js uygulamanızı ayarlama

Uygulamayı derlemek için kod yazmaya başlamadan önce, uygulamanızın çerçevesini oluşturabilirsiniz. Çerçeve kodu olan Node. js uygulamanızı ayarlamak için aşağıdaki adımları çalıştırın:

1. Sık kullandığınız terminali açın.
2. Node.js uygulamanızı kaydetmek istediğiniz klasör veya dizini bulun.
3. Aşağıdaki komutlarla iki adet boş JavaScript dosyası oluşturun:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. @No__t_0 bir dosya oluşturun ve başlatın. Aşağıdaki komutu kullanın:
   * ```npm init -y```

5. npm aracılığıyla @azure/cosmos modülünü yükleyin. Aşağıdaki komutu kullanın:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Uygulamanızın yapılandırmasını ayarlama

Artık uygulamanız mevcut olduğuna göre, Azure Cosmos DB konuştuğunu unutmayın. Aşağıdaki adımlarda gösterildiği gibi, birkaç yapılandırma ayarını güncelleştirerek uygulamanızı Azure Cosmos DB konuşacak şekilde ayarlayabilirsiniz:

1. Sık kullandığınız metin düzenleyicisinde ```config.js``` öğesini açın.

1. Aşağıdaki kod parçacığını kopyalayıp yapıştırın ve Azure Cosmos DB uç noktası URI ve birincil anahtarınıza ```config.endpoint``` ve ```config.key``` özelliklerini ayarlayın. Bu yapılandırmaların her ikisini de [Azure portalında](https://portal.azure.com) bulabilirsiniz.

   ![Azure portaldan anahtarları alma ekran görüntüsü][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.key = "~your primary key here~";
   ``` 

1. Aşağıdaki ```database```, ```container``` ve ```items``` verilerini kopyalayıp ```config.endpoint``` ve ```config.key``` özelliklerini ayarladığınız ```config``` nesnenize yapıştırın. Veritabanınızda depolamak istediğiniz veriler zaten varsa, verileri burada tanımlamak yerine Azure Cosmos DB içindeki veri geçiş aracını kullanabilirsiniz. Config. js dosyanız aşağıdaki koda sahip olmalıdır:

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   JavaScript SDK Genel terimler *kapsayıcısını* ve *öğesini*kullanır. Bir kapsayıcı koleksiyon, grafik veya tablo olabilir. Öğe de kapsayıcının içinde bulunan belge, kenar/köşe veya satır olabilir. 
   
   `module.exports = config;` kod, ```config``` nesnesini dışarı aktarmak için kullanılır, böylece ```app.js``` dosyasında başvurabilirsiniz.

## <a id="Connect"></a>Azure Cosmos DB hesabına bağlanma

1. Bir metin düzenleyicisinde boş ```app.js``` dosyanızı açın. ```@azure/cosmos``` modülünü ve yeni oluşturduğunuz ```config``` modülünü içeri aktarmak için aşağıdaki kodu kopyalayıp yapıştırın.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Yeni bir CosmosClient oluşturmak için, önceden kaydedilen ```config.endpoint``` ve ```config.key``` öğelerini kullanmak amacıyla kodu kopyalayıp yapıştırın.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });
   ```
   
> [!Note]
> **Cosmos DB öykünücüye**bağlanıyorsanız, düğüm IŞLEMINIZ için SSL doğrulamasını devre dışı bırakın:
>   ```
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Artık Azure Cosmos DB istemcisini başlatmaya yarayacak koda sahip olduğunuza göre, Azure Cosmos DB kaynaklarıyla çalışmaya bakalım.

## <a name="create-a-database"></a>Veritabanı oluşturun

1. Veritabanı KIMLIĞINI ve kapsayıcı KIMLIĞINI ayarlamak için aşağıdaki kodu kopyalayın ve yapıştırın. Bu kimlikler Azure Cosmos DB istemcisinin doğru veritabanını ve kapsayıcıyı bulmasını sağlayacaktır.

   ```javascript
   const client = new CosmosClient({ endpoint, key });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   **Veritabanları** , veritabanı sınıfının `createIfNotExists` veya Oluştur işlevi kullanılarak oluşturulabilir. Veritabanı, kapsayıcılar genelinde bölümlenmiş öğelerin mantıksal bir kapsayıcısıdır. 

2. **createDatabase** ve **readDatabase** yöntemlerini kopyalayıp app.js dosyasında ```databaseId``` ve ```containerId``` tanımlarının altına yapıştırın. **CreateDatabase** işlevi, zaten mevcut değilse ```config``` NESNESINDEN belirtilen kimlik ```FamilyDatabase``` yeni bir veritabanı oluşturur. **readDatabase** işlevi, veritabanının mevcut olduğundan emin olmak için veritabanı tanımını okur.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { resource: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Aşağıdaki kodu kopyalayın ve çıkış iletisini yazdıracak **exit** yardımcı işlevini eklemek için **createDatabase** ve **readDatabase** işlevlerini ayarladığınız yere yapıştırın. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Aşağıdaki kodu kopyalayıp **createDatabase** ve **readDatabase** işlevlerini çağırmak için **exit** işlevini ayarladığınız yere yapıştırın.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Bu noktada, ```app.js``` kodunuz şöyle görünmelidir:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { resource: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Kapsayıcı oluşturma

Ardından, verileri depolayabilmeniz ve sorgulayabilmeniz için Azure Cosmos DB hesabı içinde bir kapsayıcı oluşturun. 

> [!WARNING]
> Bir kapsayıcının oluşturulması fiyatlandırma etkilerine sahiptir. Beklendiklerinizi bilmeniz için [fiyatlandırma](https://azure.microsoft.com/pricing/details/cosmos-db/) sayfamızı ziyaret edin.

Bir kapsayıcı, **kapsayıcılar** sınıfından `createIfNotExists` veya Oluştur işlevi kullanılarak oluşturulabilir.  Kapsayıcı öğelerden (SQL API kullanıldığında JSON belgeleri) ve ilişkili JavaScript uygulama mantığından oluşur.

1. **createContainer** ve **readContainer** işlevini kopyalayıp app.js dosyasında **readDatabase** işlevinin altına yapıştırın. **createContainer** işlevi mevcut değilse ```config``` nesnesiyle belirtilen ```containerId``` bilgisine sahip yeni bir kapsayıcı oluşturur. **readContainer** işlevi, kapsayıcının mevcut olduğundan emin olmak için kapsayıcı tanımını okur.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Çağrının altındaki kodu kopyalayıp **readDatabase** hedefine yapıştırarak **createContainer** ve **readContainer** işlevlerini yürütün.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Bu noktada, ```app.js``` kodunuz şöyle görünmelidir:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Öğe oluşturma

**Öğeler** sınıfının Create işlevi kullanılarak bir öğe oluşturulabilir. SQL API 'sini kullanırken öğeler, Kullanıcı tanımlı (rastgele) JSON içeriği olan belgeler olarak yansıtılacaktır. Artık Azure Cosmos DB'ye bir öğe ekleyebilirsiniz.

1. **createFamilyItem** işlevini kopyalayıp **readContainer** işlevinin altına yapıştırın. **createFamilyItem** işlevi, ```config``` nesnesinde kaydedilen JSON verilerini içeren öğeleri oluşturur. Aynı KIMLIĞE sahip bir öğenin oluşturmadan önce mevcut olmadığından emin olmak için denetliyoruz.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. **createFamilyItem** işlevini yürütmek için **readContainer** çağrısının altına kodu kopyalayıp yapıştırın.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Azure Cosmos DB kaynaklarını sorgula

Azure Cosmos DB, her kapsayıcıda depolanan JSON belgelerine karşı zengin sorguları destekler. Aşağıdaki örnek kod, kapsayıcınızdaki belgeler için çalıştırabileceğiniz bir sorguyu gösterir.

1. **queryContainer** işlevini kopyalayıp app.js dosyasındaki **createFamilyItem** işlevinin altına yapıştırın. Azure Cosmos DB, aşağıda gösterildiği gibi SQL benzeri sorguları destekler.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { resources } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).fetchAll();
    for (var queryResult of resources) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. **queryContainer** işlevini yürütmek için **createFamilyItem** çağrısının altına kodu kopyalayıp yapıştırın.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Bir öğeyi değiştirme
Azure Cosmos DB, öğelerin içeriğini değiştirmeyi destekler.

1. **replaceFamilyItem** işlevini kopyalayıp app.js dosyasındaki **queryContainer** işlevinin altına yapıştırın. Alt öğenin 'grade' özelliğini 6 yerine 5 olarak değiştirdiğimize dikkat edin.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. **replaceFamilyItem** işlevini yürütmek için **queryContainer** çağrısının altına kodu kopyalayıp yapıştırın. Ayrıca, öğenin başarılı bir şekilde değiştiğini doğrulamak üzere **queryContainer**'ı tekrar çağırmak için kodu ekleyin.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Öğe silme

Azure Cosmos DB, JSON öğelerini silmeyi destekler.

1. **deleteFamilyItem** işlevini **replaceFamilyItem** işlevinin altına kopyalayıp yapıştırın.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. **deleteFamilyItem** işlevini yürütmek için ikinci **queryContainer** çağrısının altına kodu kopyalayın ve yapıştırın.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Veritabanını silme

Oluşturulan veritabanı silindiğinde, veritabanı ve tüm alt kaynaklar (kapsayıcılar, öğeler vb.) kaldırılır.

1. Veritabanını ve tüm alt kaynaklarını kaldırmak için **cleanup** işlevini kopyalayıp **deleteFamilyItem** işlevinin altına yapıştırın.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. **cleanup** işlevini yürütmek için **deleteFamilyItem** çağrısının altına kodu kopyalayıp yapıştırın.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Node. js uygulamanızı çalıştırma

Kodunuzun son hali şu şekilde olmalıdır:

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

Terminalinizde ```app.js``` dosyanızı bulun ve komutu çalıştırın: 

```bash 
node app.js
```

Başlarken uygulamanızın çıktısını görmeniz gerekir. Çıktı aşağıdaki örnek metinle eşleşmelidir.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Eksiksiz Node.js öğreticisi çözümünü edinme 

Bu öğreticideki adımları tamamlama fırsatınız olmadıysa veya yalnızca kodu indirmek isterseniz [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started )'dan ulaşabilirsiniz. 

Bu makaledeki tüm kodu içeren başlangıç çözümünü çalıştırmak için şunlar gerekir: 

* [Azure Cosmos DB hesabı][create-account]. 
* GitHub'da bulunan [Başlangıç](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) çözümü. 

Projenin bağımlılıklarını NPM aracılığıyla yükler. Aşağıdaki komutu kullanın: 

* ```npm install``` 

Sonra, ```config.js``` dosyasında config. Endpoint ve config. Key değerlerini [3. Adım: uygulamanızın yapılandırmalarını ayarlama](#Config)bölümünde açıklandığı gibi güncelleştirin.  

Ardından terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu kaynaklara artık ihtiyaç duyulmadığında, kaynak grubunu, Azure Cosmos DB hesabı ve tüm ilgili kaynakları silebilirsiniz. Bunu yapmak için Azure Cosmos DB hesabı için kullandığınız kaynak grubunu seçin, **Sil**' i seçin ve ardından silinecek kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Cosmos DB bir hesabı izleme](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
